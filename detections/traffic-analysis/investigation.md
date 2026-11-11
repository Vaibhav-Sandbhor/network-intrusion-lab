\# Investigation: Wireshark Packet Analysis of Nmap Scan



\## Summary

Captured live traffic during an Nmap SYN scan (-sS -sV) against

Metasploitable2, then analyzed the resulting PCAP in Wireshark to

confirm the scan's signature at the packet level and extract IOCs.



\## Capture details

\- Capture file: nmap\_scan.pcapng

\- Total packets captured: 2477

\- Filtered to SYN-only probes (tcp.flags.syn==1 \&\& tcp.flags.ack==0):

&#x20; 1047 packets matched (42.3% of total capture)



\## IOCs extracted

| Indicator | Value |

|---|---|

| Attacker IP | 192.168.160.128 |

| Target IP | 192.168.160.129 |

| Source port (static across scan) | 55261 |

| Packet signature | TCP SYN, Seq=0, Win=1024, MSS=1460 |

| Scan window | \~10ms between dozens of distinct destination ports |

| Notable ports probed | 21, 22, 25, 53, 80, 110, 135, 199, 554, 993, 995, 1025, 1723, 5900, 8888 |



\## Analysis

Every SYN probe shares an identical packet fingerprint (Seq=0, Win=1024,

MSS=1460) and originates from the same source port (55261) — only the

destination port changes. Combined with the extremely tight timing

between probes (multiple distinct ports contacted within single-digit

milliseconds), this is a clear automated port-scan signature,

distinguishable from legitimate client traffic, which would show varying

window sizes, randomized source ports per connection, and natural human-

scale timing.



\## Notes

\- This packet-level view corroborates the Suricata alert from the

&#x20; port-scan detection (detections/port-scan/) — Suricata's alert is the

&#x20; automated detection layer, while this Wireshark analysis is the manual

&#x20; deep-dive an analyst would perform to confirm and document the finding.

\- The consistent source port (55261) throughout the scan is itself an

&#x20; IOC — a legitimate client opening many connections would typically use

&#x20; a new ephemeral source port per connection, not reuse one across

&#x20; dozens of targets.



\## Next steps

\- Follow a TCP stream on one of the open-port responses to extract

&#x20; service banner text

\- Compare packet signature against a legitimate multi-connection client

&#x20; (e.g. a browser loading a page with multiple assets) to contrast

&#x20; scan traffic vs normal traffic patterns

