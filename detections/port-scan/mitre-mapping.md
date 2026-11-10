\# MITRE ATT\&CK Mapping



\*\*Technique:\*\* T1046 — Network Service Discovery

\*\*Related:\*\* T1595.001 — Active Scanning: Scanning IP Blocks



\## Description

Adversaries scan target networks to identify open ports, running services,

and potential entry points before attempting exploitation. Nmap is the

industry-standard tool for this reconnaissance phase.



\## Data source

Suricata network IDS, TCP flow + flag-based detection



\## Why this detection works

A single SYN packet is normal (any new connection starts this way). The

signal is in the rate and spread — 10+ SYN packets to different ports from

one source within a 10-second window is not normal client behavior; it's

consistent with automated port scanning tools like Nmap.

