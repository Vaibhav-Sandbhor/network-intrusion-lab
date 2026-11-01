\# Environment Setup



\## VMs



| VM | Role | Network |

|---|---|---|

| Kali Linux | Attacker + IDS sensor (Suricata) | Host-only (VMnet1), temporarily NAT for package installs |

| Metasploitable2 | Target | Host-only (VMnet1) |

| Windows 10 / Ubuntu | Shared from SOC lab | Host-only (VMnet1) |



\## Incident: Host-only subnet silently changed



During setup, the VMware Host-only network (VMnet1) was observed handing

out addresses on 192.168.254.0/24 initially, then later on 192.168.160.0/24,

after modifying settings in VMware's Virtual Network Editor (clicking

"Restore Defaults" while troubleshooting an unrelated DHCP issue).



Symptoms:

\- New VMs (e.g. a freshly attached Kali adapter) got the new subnet

&#x20; automatically via DHCP

\- A manually-assigned static IP on the old subnet (192.168.254.140)

&#x20; appeared valid locally (ip a showed it, routes looked correct) but

&#x20; all ARP requests returned <incomplete> — the static IP was assigned

&#x20; to a subnet that no longer existed on the virtual switch



Diagnosis process:

1\. Ruled out "target machine powered off" (checked service status)

2\. Ruled out DHCP service failure (checked VMware DHCP/NAT services

&#x20;  were running)

3\. Ruled out VM-level misconfiguration (checked adapter was correctly

&#x20;  set to Custom: VMnet1 in VM settings)

4\. Checked the HOST's own VMware virtual adapter via `ipconfig /all`

&#x20;  on Windows — found "VMware Network Adapter VMnet1" was itself on

&#x20;  192.168.160.1, not 192.168.254.1 as the Virtual Network Editor

&#x20;  display suggested

5\. Reset all VMs to DHCP (auto) instead of static IPs — all machines

&#x20;  converged on the real current subnet (192.168.160.0/24) and

&#x20;  connectivity was restored



\## Lesson

VMware's Virtual Network Editor UI can display a subnet that doesn't

match what's actually bound on the host's virtual adapter, especially

after "Restore Defaults." Always verify the live subnet via

`ipconfig /all` (host) or `ip a` (guest) rather than trusting the editor

UI or assuming a previously-confirmed IP is still valid. Static IPs are

risky in a lab where the underlying virtual network can shift — DHCP

with periodic verification is more resilient.

