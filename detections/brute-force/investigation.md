\# Investigation: FTP Brute Force Detection



\## Summary

Simulated a brute-force attack against Metasploitable2's FTP service

(vsftpd, port 21) from Kali using Hydra and rockyou.txt, targeting the

default msfadmin account. Built a Suricata rule to detect the resulting

failed-login pattern.



\## Timeline

\- Multiple bursts of failed FTP logins detected, alerting every \~10

&#x20; seconds while the attack was active (e.g. 03:14:04, 03:14:14,

&#x20; 03:15:17, 03:16:17), each representing 5+ failed attempts grouped

&#x20; by the threshold rule



\## Detection — debugging journey

This rule required several iterations to get right, each a genuine

troubleshooting lesson:



1\. \*\*Wrong direction\*\*: initial rule matched traffic FROM the attacker

&#x20;  TO the server. The actual "Login incorrect" string is sent BY the

&#x20;  server (Metasploitable, port 21) TO the client (Kali) — i.e. the

&#x20;  reply, not the request. Rule had to target `tcp any 21 -> any any`,

&#x20;  not the reverse.

2\. \*\*Sticky buffer syntax\*\*: attempted to use `ftp.completion\_code:"530"`

&#x20;  as if it took an inline value; Suricata's app-layer sticky buffers

&#x20;  (ftp.completion\_code, ftp.reply, etc.) require a separate `content`

&#x20;  match after declaring the buffer, not an inline argument.

3\. \*\*Rule file path mismatch\*\*: same issue as the port-scan detection —

&#x20;  suricata.yaml's default-rule-path is /var/lib/suricata/rules/, so

&#x20;  edits to /etc/suricata/rules/local.rules had to be copied over before

&#x20;  each restart.

4\. \*\*Orphaned processes\*\*: multiple Suricata instances accumulated across

&#x20;  restarts (Ctrl+C in a terminal didn't always kill the backgrounded

&#x20;  process), causing an af-packet "fanout not supported" error and

&#x20;  silently broken packet capture, since instances were competing for

&#x20;  the same interface. Diagnosed via `ps aux | grep suricata` showing

&#x20;  3 separate running instances; resolved with `kill -9` on each PID

&#x20;  before a clean restart. Thread count (W:2 vs W:1) was a useful signal

&#x20;  that only one process was properly bound after the fix.



\## Notes

\- Metasploitable's FTP service runs vsftpd in a deliberately weak

&#x20; configuration — a real target would likely have account lockout

&#x20; policies that this attack would trip much faster.

\- `track by\_dst` was required (not `by\_src`) because the alerting

&#x20; packets originate from the server; the attacker is the destination

&#x20; of these specific packets, so grouping had to track the destination

&#x20; IP to correctly identify the attacker.



\## Next steps

\- Add a matching rule for SSH brute-force (port 22) for comparison

\- Test whether lowering the threshold count triggers on a slower,

&#x20; evasive brute-force attempt



