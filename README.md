\# homelab101



\## Goal

Hey, I'm Nour, a first year cybersecurity student, and this project is something I started to learn by doing. I'm building a simple home lab from scratch:

An isolated virtual network where KaliLinux and Ubuntu VMs talk to each other but not to the internet or my home network.

I'm using it to practice networking, Linux, and (soon) scanning and attacking intentionally vulnerable machines in a safe environment. I'm documenting every step, including the mistakes and fixes, because my ultimate goal is to grow into a cloud security/DevSecOps engineer.

\## Architecture



| VM | OS | Role | Adapters | IP |

|----|----|------|----------|----|

| Kali | Kali Linux | Attacker | NAT + Internal (homelabnetwork) | 192.168.56.10 |

| Ubuntu | Ubuntu | General machine | Internal (homelabnetwork) | 192.168.56.19 |

| VulnerableMachine | Metasploitable 2 | Target | Internal (homelabnetwork) | 192.168.56.30 |



Network: VirtualBox Internal Network named `homelabnetwork`, subnet 192.168.56.0/24, no DHCP, no internet.



\## Setup



\### Adding the vulnerable target

1\. Downloaded Metasploitable 2 from the official SourceForge page and extracted the ZIP.

2\. Created a VM (Linux, 32-bit, 512 MB RAM) with no ISO, attaching the existing `Metasploitable.vmdk` as its disk.

3\. Set Adapter 1 to Internal Network `homelabnetwork` before first boot, so it was never exposed to my real network.

4\. Logged in with the default `msfadmin` / `msfadmin` credentials.

5\. Switched the console to AZERTY with `sudo loadkeys fr`.

6\. Set a static IP by editing `/etc/network/interfaces` (static, address 192.168.56.30, netmask 255.255.255.0).



\## Testing and verification

\- From Kali: `ping -c 5 192.168.56.30` got replies.

\- Isolation check: (add the result of `ping -c 3 8.8.8.8` from the target, it should fail)



\## Troubleshooting log

\- \*\*Empty folder in the VirtualBox file picker:\*\* the ZIP wasn't extracted yet. Extracted it and the `.vmdk` appeared.

\- \*\*No `inet addr` in `ifconfig`:\*\* the static config hadn't been applied. Re-applied it and the ping worked.

