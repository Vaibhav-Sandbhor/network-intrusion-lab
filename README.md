\# Network Intrusion Detection \& Traffic Analysis Lab



A simulated network security monitoring environment built to practice

packet analysis, IDS rule development, and traffic-based threat detection.



\## Architecture



\- \*\*Kali Linux\*\* — attacker + IDS sensor (Suricata), dual-homed: NAT for

&#x20; internet/package installs, Host-only (VMnet1) for lab traffic

\- \*\*Metasploitable2\*\* — intentionally vulnerable target, Host-only network

\- \*\*Windows 10 / Ubuntu\*\* — shared with the SOC detection lab, same

&#x20; isolated network segment



All lab traffic isolated on a VMware Host-only virtual network

(192.168.160.0/24 at time of writing — see setup notes on why this

shifted from the original 192.168.254.0/24).



\## What this demonstrates



\- IDS rule development in Suricata

\- Packet capture and analysis in Wireshark

\- Network-based attack simulation (reconnaissance, brute-force)

\- IOC extraction from traffic

\- Real-world VMware networking troubleshooting (see /setup)



\## Detections planned


## Detections built

| Technique | MITRE ATT&CK | Status |
|---|---|---|
| Port scan / reconnaissance (Nmap) | T1046 | Complete |
| Brute-force over network (Hydra vs Metasploitable FTP) | T1110 | Complete |
| Traffic analysis & IOC extraction (Wireshark) | - | Complete |



\## Repo structure



\- `setup/` — environment build, including a real VMware networking

&#x20; incident where the Host-only subnet silently changed

\- `detections/` — one folder per technique, with Suricata rule,

&#x20; PCAP analysis notes, and IOCs extracted

\- `screenshots/` — supporting evidence

