# Meow — HackTheBox Writeup

**Difficulty:** Easy
**OS:** Linux
**Date:** 07/July/2026
**Status:** Pwned ✓

---

## Summary

Meow is a "Very Easy" Starting Point machine that only exposes a Telnet service. The core vulnerability is a misconfiguration: the `root` account has no password set, allowing direct, unauthenticated administrative access over Telnet.

---

## Reconnaissance

No port scan was run for this box — Meow is a "Very Easy" Starting Point machine and the walkthrough material made clear only Telnet (port 23) would be open, so I connected directly.

```bash
telnet 10.129.170.62
```

**Key findings:**

* Port 23/tcp — `telnet` open, presenting a `Meow login:` prompt

> Note: In a real engagement (or any box past Starting Point difficulty), always run `nmap -sV <IP>` first rather than assuming what's open — I only skipped it here because the target was already known to be Telnet-only.

---

## Enumeration

Telnet is a legacy remote-administration protocol that transmits everything, including credentials, in plaintext. Connecting returned a "Hack the Box" ASCII banner and a login prompt, so the next step was to test credentials directly.

---

## Exploitation

Tried logging in as `root` and, when prompted for a password, just pressed Enter (submitted blank).

```bash
login: root
password: [pressed Enter — blank password]
```

The target accepted `root` with **no password at all**, logging straight in as superuser — a critical misconfiguration that skips authentication entirely.

---

## Privilege Escalation

Not applicable — the Telnet login granted `root` access immediately, so no further privilege escalation was required.

---

## Flags

```bash
ls
# flag.txt  snap

cat flag.txt
```

Copied the 32-character hex string and submitted it on the HTB Starting Point page.

* Flag: `b40abdfe23665f766f9c61ecba8a4c19`

---

## Lessons Learned

* Telnet is unencrypted and considered obsolete/insecure for remote administration — it should never be exposed on a network, especially not with weak or blank credentials.
* Always try a small list of default/blank credentials (`admin`, `administrator`, `root`) early when a login service is the only thing available — it's fast and sometimes immediately effective.
* For future machines, always run `nmap -sV <IP>` (or `-sC -sV`) first instead of assuming the open ports — I got away with skipping it here only because Meow is a known, single-service beginner box.
* Reinforced the basic pentest workflow: **Recon → Enumerate → Exploit → Loot the flag**, even on a one-port box.
