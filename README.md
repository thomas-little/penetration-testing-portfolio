
# Penetration Testing Portfolio

A collection of authorized security assessments conducted
in controlled laboratory environments.

The projects document technical reconnaissance,
vulnerability identification, exploitation validation,
log analysis, remediation, and retesting.

## Projects

| Project | Environment | Focus | Status |
|---|---|---|---|
| [01 — Network Exposure to Initial Access](projects/01-network-exposure-initial-access/README.md) | AlmaLinux 9.8 | Enumeration, HTTP credential exposure, SSH initial access | Complete |
| 02 — Web Application Exploitation | OWASP Juice Shop | Web vulnerabilities and exploitation | Planned |
| 03 — Linux Privilege Escalation | AlmaLinux 9.8 | Local misconfiguration and privilege escalation | Planned |

## Featured Assessment

### Project 01 — Network Exposure to Initial Access

An intentionally vulnerable Apache configuration exposed
a laboratory backup file containing SSH credentials.

The assessment demonstrated a sequence from network
enumeration to authenticated access as a non-root user.

Credential rotation and HTTP retesting were conducted.
The tested HTTP resources returned 404 responses,
and the former SSH password was rejected.

[Read the case study](projects/01-network-exposure-initial-access/README.md)

[View the complete PDF report](projects/01-network-exposure-initial-access/Project1_Network_Exposure_Initial_Access.pdf)

## Skills Demonstrated

- Network reconnaissance and service enumeration
- Linux and Apache configuration assessment
- Credential exposure analysis
- Authorized SSH access validation
- System and application log analysis
- Remediation testing and verification
- Technical report writing
- MITRE ATT&CK technique mapping

## Laboratory Environment

Assessments were conducted against systems owned
and controlled for security training.

No unauthorized third-party testing was performed.

## Reporting

Reports include assessment scope, methodology,
technical evidence, findings, remediation recommendations,
and verification limitations.