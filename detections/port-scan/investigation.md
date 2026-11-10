\# Investigation: Nmap SYN Scan Detection



\## Summary

Simulated reconnaissance against Metasploitable2 (192.168.160.129) from

Kali (192.168.160.128) using an Nmap SYN scan (-sS), identifying 23 open

ports including several high-risk legacy services (Telnet, rsh/rlogin,

NFS, VNC, and the well-known "ingreslock" backdoor on port 1524).



\## Timeline

\- 2026-11-10 01:35:49 — Suricata alert fired: "Possible Nmap Port Scan

&#x20; Detected" (sid:1000001), triggered by rapid SYN packets from

&#x20; 192.168.160.128 across multiple destination ports on 192.168.160.129



\## Detection

Wrote a custom Suricata rule using threshold-based detection (10+ SYN

packets from one source within 10 seconds) rather than matching on any

single packet, to reliably distinguish a scan from normal connection

traffic while minimizing false positives.



\## Notes

\- Suricata's default rule path is /var/lib/suricata/rules/, NOT

&#x20; /etc/suricata/rules/ as commonly documented online. Custom rules must

&#x20; either be placed in the default path or suricata.yaml's

&#x20; default-rule-path must be changed — a real deployment gotcha.

\- Suricata must be actively running (not started before/after) during the

&#x20; traffic generation for the capture to occur — timing matters when

&#x20; testing detections live versus against a saved PCAP.

\- The scan revealed Metasploitable exposes port 1524 (ingreslock), a

&#x20; famous intentional backdoor in this training image — worth flagging as

&#x20; a critical finding in a real engagement context.



\## Next steps

\- Extract the full alert JSON as an IOC reference

\- Write a second rule using Suricata's built-in `flow:stateless` or

&#x20; `detection\_filter` for comparison against the threshold approach

\- Capture the scan traffic as a PCAP and analyze it in Wireshark

