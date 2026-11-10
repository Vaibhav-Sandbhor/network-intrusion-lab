\# MITRE ATT\&CK Mapping



\*\*Technique:\*\* T1110 — Brute Force

\*\*Sub-technique:\*\* T1110.001 — Password Guessing



\## Description

Adversaries attempt to gain access via automated password guessing against

exposed network services (here, FTP).



\## Data source

Suricata network IDS, TCP payload content matching on FTP server replies



\## Why this detection works

The rule matches the literal "Login incorrect" string sent by the FTP

server (direction: server to client, port 21 as source), which only

appears on failed authentication. Grouping 5+ occurrences to the same

destination (the attacker) within 10 seconds distinguishes automated

brute-forcing from an occasional manual mistyped password.

