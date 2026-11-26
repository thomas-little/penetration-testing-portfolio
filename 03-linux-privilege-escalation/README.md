# Project 03 — Linux Privilege Escalation Through Insecure Cron Script Permissions

**Authorized Linux security laboratory | AlmaLinux 10.3 (Mauve Lion) | 26 November 2026**

> An independent, intentionally configured privilege-escalation lab on a controlled VMware virtual machine. The assessment did not involve employer infrastructure, other users' systems, or a production service. Initial access to the Linux host was **provisioned** for the exercise; it was not obtained through remote exploitation.

## Outcome at a glance

This assessment tested whether a nonadministrative user could modify a maintenance script that was subsequently executed by a root-owned cron job. **The privilege boundary failure was confirmed**: a proof command appended by the `p3user` account was executed in the root context and produced a root-owned evidence file showing `cron-euid=0 cron-user=root`. After restoring the script and changing its owner/group and permissions, write access was no longer available to `p3user`, the legitimate cron heartbeat continued, and the privileged proof file was not recreated.

| Assessment item | Result | Evidence |
|:--|:--|:--|
| Target baseline | AlmaLinux 10.3, VMware, `192.168.1.101`, SELinux enforcing | [Figure 1](evidence/01-target-baseline.png) |
| Scheduler | `crond` active; root task executing a maintenance script every minute | [Figure 2](evidence/02-crond-service.png), [Figure 5](evidence/05-root-cron-definition.png) |
| Privilege prerequisite | `p3user` in `p3editors` group; script owned by `root:p3editors` with `0775` permissions | [Figure 3](evidence/03-user-group-baseline.png), [Figure 4](evidence/04-insecure-script-permission.png) |
| Vulnerability | **HIGH (laboratory severity)** — a low-privileged group could modify a root-executed file | [Figure 8](evidence/08-writable-script-discovery.png) |
| Privilege boundary | **Confirmed** — root-owned proof file with EUID 0 and user `root` | [Figure 11](evidence/11-script-modification.png), [Figure 12](evidence/12-root-execution-proof.png) |
| Remediation | Restored maintenance script, `root:root` ownership, and `0755` permissions | [Figure 14](evidence/14-corrected-script-permissions.png) |
| Retest | **Passed for the tested path** — no write access; cron kept running; proof absent | [Figure 15](evidence/15-write-permission-retest.png), [Figure 16](evidence/16-remediation-verification.png) |

**[Read the complete penetration testing report (PDF)](Project3_Linux_Privilege_Escalation.pdf)**

## Vulnerability and attack chain

The root-owned cron definition at `/etc/cron.d/project3-maintenance` executed `/usr/local/bin/project3-maintenance.sh` once per minute. Although the script was owned by root, it was assigned to the `p3editors` group with group-write permission. The nonadministrative `p3user` account was a member of that group. As a result, modification of the script did not require administrator authorization.

From a provisioned `p3user` SSH session, writable-file enumeration identified the maintenance script. Examination of the cron definition established that the same file would execute as root. The account could not directly list the root-only `/var/lib/project3-lab` directory, providing a meaningful baseline for the subsequent proof. A short identity diagnostic was appended to the script without using `sudo`. Following scheduled execution, the file `/var/lib/project3-lab/privilege-proof.txt` was observed with **root ownership** and the content `cron-euid=0 cron-user=root`.

This result demonstrates controlled root-context code execution through an unsafe scheduled-task dependency. It **does not** demonstrate an interactive root shell, credential theft, persistence, or compromise of a remote service.

### Classification

- **Finding:** P3-F01 — Privilege Escalation via Group-Writable Root-Executed Cron Script
- **Severity:** High, contextual laboratory assessment; no formal CVSS vector assigned
- **CWE:** [CWE-732 — Incorrect Permission Assignment for Critical Resource](https://cwe.mitre.org/data/definitions/732.html)
- **MITRE ATT&CK:** [T1053.003 — Scheduled Task/Job: Cron](https://attack.mitre.org/techniques/T1053/003/)

## Remediation and retesting

The intentionally vulnerable maintenance script was restored to its baseline heartbeat-only logic. Ownership was changed to `root:root`, and file permissions were set to `0755`. A subsequent test under `p3user` showed `PASS: Script is not writable`. The existing proof file was removed, cron continued generating new legitimate heartbeat records, and the post-change test returned `PASS: Privilege proof not recreated`.

The observed success applies to this **specific file modification pathway**, not to every privileged task or filesystem permission on the host. A broader hardening program would audit all code executed by root-owned schedulers, including writable parent paths and symlink targets, and establish change monitoring for critical scripts.

## Evidence qualifications

The screenshots include details that are relevant to responsible reporting:

- A shell command using `test -z` without the correct operand returned a usage error in the initial enumeration capture. That command is **not** counted as a valid write-permission check. The `find` result, permission listing, and actual unprivileged script modification independently support writability.
- Cron audit records include intermittent `unexpected EOF while looking for matching` quote errors. The screenshots do not conclusively establish the full sequence of edits behind those errors. Their presence means that successful completion of every scheduled execution cannot be asserted. The root-owned proof file remains direct evidence of the controlled root-context operation.
- The initial `sudo -n id` check required a password. It establishes that **passwordless sudo did not succeed**, not that every form of sudo use was independently ruled out.
- SELinux was recorded as Enforcing. No claim of an SELinux bypass is made.

## Tools and environment

**AlmaLinux 10.3**, **VMware**, **Kali Linux**, **OpenSSH**, **Cronie**, Bash, `stat`, `find`, Linux DAC permission checks, cron/journal logs. Test system: `192.168.1.101`. The intentionally configured group: `p3editors`; starting user: `p3user`.

## Reports and supporting files

- [Full report — PDF](Project3_Linux_Privilege_Escalation.pdf), including methodology, numbered figures, attack narrative, finding, logging analysis, remediation, and an evidence register.
- [Evidence directory](evidence/), containing the 17 original supplied screenshots for the 16 numbered checkpoints (cron logging has two figures).
- [References](references.md), containing classification and original-report-template links.
- [Noraj template MIT license](NORAJ_TEMPLATE_LICENSE.txt), retained for attribution.

## Report attribution

The report organization adapts [Alexandre ZANNI (noraj)'s Markdown penetration-test report template](https://github.com/noraj/OSCP-Exam-Report-Template-Markdown), released under the MIT license. This portfolio project is **not** an OSCP examination submission or an external client engagement.
