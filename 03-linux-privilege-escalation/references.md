# References — Project 03

These are the conceptual references used for the technique mapping, weakness classification, and report-template attribution. Primary evidence consists of the supplied laboratory screenshots.

1. [MITRE CWE-732 — Incorrect Permission Assignment for Critical Resource](https://cwe.mitre.org/data/definitions/732.html). Used to classify improper group-write access to privileged script code.
2. [MITRE ATT&CK T1053.003 — Scheduled Task/Job: Cron](https://attack.mitre.org/techniques/T1053/003/). Used to identify the scheduled execution mechanism; not a claim of real-world intrusion.
3. [Alexandre ZANNI (noraj), OSCP-Exam-Report-Template-Markdown](https://github.com/noraj/OSCP-Exam-Report-Template-Markdown). Source of the MIT-licensed reporting template structure (adapted to a laboratory assessment); its license is included with the portfolio files.
4. Relevant on-host documentation: `man 1 find`, `man 1 stat`, `man 1 test`, `man 5 crontab`, `man 8 crond` where installed.
