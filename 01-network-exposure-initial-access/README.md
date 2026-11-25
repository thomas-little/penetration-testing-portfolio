# Project 01 — Network Exposure to Initial Access

**Authorized lab penetration test | AlmaLinux 9.8 (Olive Jaguar) | 19 November 2026**

> This assessment was conducted against an intentionally vulnerable, authorized laboratory virtual machine. No employer, public host, or third-party system was tested. The training password has been redacted from the public evidence.

## Outcome at a glance

The assessment demonstrated how an Apache misconfiguration could turn an exposed backup into a valid Linux login. Enumeration of the AlmaLinux virtual machine identified an unauthenticated directory listing. A fictional laboratory backup was retrieved and found to contain an SSH account credential, which was subsequently used to authenticate as the unprivileged `labtrainee` user. Server-side logs were examined, the exposed credential was rotated, and negative HTTP and SSH retests verified that the demonstrated access paths were closed.

| Stage | Result | Evidence |
|:--|:--|:--|
| Discovery | TCP/22 and TCP/80 were reachable | [Port scan](evidence/09-nmap-port-verification.png) |
| Enumeration | OpenSSH 9.9; Apache httpd 2.4.62 | [Service scan](evidence/10-nmap-service-enumeration.png) |
| Exposure | `Options +Indexes` and `Require all granted` on `/backups/` | [Apache configuration](evidence/07-vulnerable-apache-configuration.png) |
| Sensitive file | Public listing contained a backup with a training credential | [Browser listing](evidence/11-exposed-directory-listing.png) |
| Initial access | SSH session succeeded as UID 1001 (`labtrainee`) | [Interactive session](evidence/13-successful-ssh-initial-access.png) |
| Defensive evidence | HTTP 200 retrieval and SSH password acceptance logged | [Apache logs](evidence/14-apache-access-logs.png) · [SSH logs](evidence/15-ssh-authentication-logs.png) |
| Retest | Specific HTTP paths returned 404; old password was denied | [HTTP retest](evidence/18-http-remediation-retest.png) · [SSH retest](evidence/17-ssh-credential-reset.png) |

## Attack-chain narrative

1. **Target identification.** Operating-system and interface details were recorded, followed by a baseline review of services and firewall rules. The target reported AlmaLinux 9.8 running in VirtualBox, with SELinux enforcing.
2. **Service discovery.** Nmap confirmed TCP/22 and TCP/80 were reachable and identified the SSH and HTTP services.
3. **Web enumeration.** Apache's intentionally vulnerable backup directory allowed public access and automatic indexing, exposing a plaintext backup file.
4. **Initial-access validation.** The file contained a fictional training account and password. The disclosed credential enabled SSH authentication, and the resulting session was confirmed as a non-root account. Privilege escalation was not attempted.
5. **Log correlation.** Apache access records captured backup retrieval, while `sshd` recorded accepted password authentication from the assessment workstation.
6. **Remediation and retesting.** The lab credential was rotated, subsequent authentication using the former password failed, and HTTP requests for the directory and backup file returned 404.

## Findings and fixes

**High — Plaintext credential exposed through HTTP.** An unauthenticated visitor could obtain a password that also worked for network SSH access. Recommended controls: remove secrets from web roots, rotate exposed credentials, use protected secrets management, and limit SSH account privileges.

**Medium — Apache directory indexing.** `Options +Indexes` combined with `Require all granted` let the visitor discover a private backup filename. Recommended controls: disable unneeded indexing and deny access to private paths; blocking index generation by itself does not stop direct requests for known file names.

**Verification limitation:** the recorded cleanup commands returned “No such file or directory.” Subsequent HTTP 404 responses confirm that the tested URLs were no longer retrievable; however, the effective Apache configuration and final file-system state were not recorded. Additional verification should inspect active configuration, alternative paths, and potential aliases.

## Tools and methods

- Nmap / Zenmap — host and service discovery
- `curl` and a browser — HTTP response inspection and file retrieval
- OpenSSH — authorized credential-based initial-access validation
- `journalctl` / Apache access logs — defensive trace reconstruction
- `firewall-cmd`, `apachectl` and `passwd` — lab configuration, remediation, and checks

Technique alignment: [MITRE ATT&CK T1552.001](https://attack.mitre.org/techniques/T1552/001/) (Credentials in Files) and [T1078.003](https://attack.mitre.org/techniques/T1078/003/) (Local Accounts).

## Assessment conclusions

The assessment established a reproducible cause-and-effect chain from service discovery to authenticated access, followed by negative verification of the tested access paths. The compromised account was **not root**, and an HTTP 404 response does **not** independently establish that the underlying configuration has been comprehensively audited.

The accompanying full report provides the executive summary, technical walkthrough, two formal findings, evidence register, and remediation qualifications in **`Project1_Network_Exposure_Initial_Access.md`** and PDF formats.

## Credits

The accompanying full report's organization was adapted from [noraj's MIT-licensed OSCP Exam Report Template in Markdown](https://github.com/noraj/OSCP-Exam-Report-Template-Markdown); this is an independent lab portfolio piece, **not** an OSCP exam submission or an actual client engagement.
