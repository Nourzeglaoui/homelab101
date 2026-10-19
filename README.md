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

\- The target machine network settings: (screenshots/target-network-settings.png)

\- From Kali: `ping -c 5 192.168.56.30` got replies. (screenshots/ping-kali-to-target.png)

\- Isolation check: (add the result of `ping -c 3 8.8.8.8` from the target, it should fail)



\## Troubleshooting log

\- \*\*Empty folder in the VirtualBox file picker:\*\* the ZIP wasn't extracted yet. Extracted it and the `.vmdk` appeared.

\- \*\*No `inet addr` in `ifconfig`:\*\* the static config hadn't been applied. Re-applied it and the ping worked.

### Metasploitable 2 setup

- **Empty folder in VirtualBox file picker:** the ZIP hadn't been extracted yet. 
  Fixed by extracting it fully before browsing to the .vmdk.

- **No "ISO Image" needed:** Metasploitable ships as a ready-made disk (.vmdk), 
  not an installer. Left ISO Image empty and attached the .vmdk directly as 
  an existing hard disk.

- **"Skip Unattended Installation" checkbox greyed out:** harmless in this 
  VirtualBox version when no ISO is selected. Continued past it with no issue, 
  since attaching an existing disk skips the install flow anyway.

- **No inet addr after editing /etc/network/interfaces:** the static config 
  hadn't been applied yet. Fixed with `sudo ifdown eth0 && sudo ifup eth0` 
  (a reboot also works).

- **Keyboard reset to QWERTY on every reboot:** `loadkeys fr` only lasts for 
  the session. Fixed permanently by adding `loadkeys fr` to /etc/rc.local, 
  above the `exit 0` line.

- **Major incident: lost all configuration after a reboot.** Cause: a 
  VirtualBox snapshot was restored instead of a normal reboot, most likely 
  via "Restore current snapshot" being ticked in the shutdown dialog. 
  All work since the clean-install snapshot was wiped. The VM was deleted 
  and rebuilt from scratch.
  
  Lesson learned: never let "Restore current snapshot" be ticked when closing 
  a VM unless you intend to discard changes. Use "Power off the machine" for 
  a normal shutdown instead.

- **sudo poweroff / halt showed scary errors ("unable to load IDE devices", 
  "System halted"):** cosmetic warnings from Metasploitable's outdated drivers 
  reacting to VirtualBox's virtual hardware. The shutdown still completes 
  correctly; "System halted" is the normal final message, not an error.

- **New habit adopted after the rebuild:** verify configuration survives a 
  reboot (static IP + keyboard layout) *before* taking a "clean" snapshot, 
  and take a fresh snapshot after every milestone, not just once at the start.

