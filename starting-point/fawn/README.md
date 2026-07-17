# Fawn — HackTheBox Writeup
**Difficulty:** Easy
**OS:** Linux
**Date:** 17/07/2026
**Status:** Pwned ✓
---
## Summary
Fawn is a "Very Easy" Starting Point machine that only exposes an FTP service. The core vulnerability is a misconfiguration: anonymous FTP login is enabled, allowing unauthenticated access to files on the server, including the flag.
---
## Reconnaissance
```bash
ping -c 4 10.129.207.250
nmap -sV -p 21 10.129.207.250
```
**Key findings:**
* Port 21/tcp — `ftp` (vsftpd 3.0.3)
* Host OS: Unix
---
## Enumeration
FTP can be configured to allow anonymous login — a legitimate feature meant for public file shares, but a vulnerability when left enabled on a server that shouldn't be publicly accessible. With only port 21 open, anonymous login was the obvious next thing to try.
---
## Exploitation
Connected via FTP and logged in as `anonymous` with a blank password.
```bash
ftp 10.129.207.250
# Username: anonymous
# Password: [pressed Enter — blank password]
```
Server response:
```
331 Please specify the password.
230 Login successful.
```
Anonymous credentials were accepted with no password required, granting an authenticated FTP session.
---
## Privilege Escalation
Not applicable — anonymous FTP access directly exposed the flag file, so no further privilege escalation was required.
---
## Flags
```bash
ftp> ls
# -rw-r--r--  1 0  0  32 Jun 17 15:23 flag.txt
ftp> get flag.txt
ftp> quit
cat flag.txt
```
Copied the 32-character hex string and submitted it on the HTB Starting Point page.
* Flag: `<your actual flag hash>`
---
## Lessons Learned
* Anonymous FTP login is a real, sometimes-intentional feature — the risk comes from leaving it enabled where it shouldn't be, not from the feature itself.
* FTP requires downloading files locally (`get`) before reading them — unlike Telnet/SSH, you're not viewing files directly on the remote system.
* `nmap -sV -p 21` is fine when a port is already known/suspected, but on unfamiliar targets a full port scan should come first.
* Reinforced the basic pentest workflow: **Recon → Enumerate → Exploit → Loot the flag**, even on a one-port box.
