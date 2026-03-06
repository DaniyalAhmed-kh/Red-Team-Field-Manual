# Red Team Field Manual (RTFM)
### Operator Edition — 2025

A complete adversary simulation framework covering 16 operational phases and an A–Z encyclopedia of red team concepts, techniques, and tooling. Built for practitioners doing real engagements against mature, EDR-heavy, enterprise environments — not lab boxes.

---

## What this is

Most red team resources are either too academic (here's the MITRE page for Pass-the-Hash) or too tooling-focused (here's how to run Mimikatz). This fills the gap: the operational thinking, sequencing, prioritization, and hard-won context that turns a list of techniques into a coherent engagement.

The framework is a single self-contained HTML file. No dependencies, no build step, no framework. Open it in a browser and use it.

---

## Contents

**16-Phase Operational Framework**

| Phase | Name | MITRE Tactic |
|-------|------|-------------|
| 00 | Planning & Scoping | PRE-ATT&CK |
| 01 | Reconnaissance & OSINT | TA0043 |
| 02 | Weaponization | TA0042 |
| 03 | Initial Access | TA0001 |
| 04 | Execution | TA0002 |
| 05 | Persistence | TA0003 |
| 06 | Privilege Escalation | TA0004 |
| 07 | Defense Evasion | TA0005 |
| 08 | Credential Access | TA0006 |
| 09 | Internal Discovery | TA0007 |
| 10 | Lateral Movement | TA0008 |
| 11 | Collection | TA0009 |
| 12 | Command & Control | TA0011 |
| 13 | Exfiltration | TA0010 |
| 14 | Impact Simulation | TA0040 |
| 15 | Reporting & Debrief | Post-Engagement |

**A–Z Operator Encyclopedia**

26 entries written in plain operator language covering the concepts, mindsets, and techniques that don't fit neatly into a phase checklist.

```
A — Active Directory         N — NTLM Relay
B — BloodHound               O — OPSEC
C — C2 Profile Design        P — Phishing in 2025
D — DCSync                   Q — Quick Wins
E — EDR Bypass Mindset       R — Redirectors
F — Fileless Operation       S — Syscalls
G — Golden Ticket            T — Threat Intelligence
H — HTML Smuggling           U — Unconstrained Delegation
I — Implant Architecture     V — Vulnerability Chaining
J — Jump Hosts & Pivoting    W — WMI
K — Kerberoasting            X — XXE & SSRF
L — LSASS Without Getting Caught   Y — Your Report Changes Things
M — MFA — It's Not a Hard Stop     Z — Zero Day Mindset
```

---

## Standards Alignment

- **MITRE ATT&CK v15** — every technique tagged with TTP ID
- **PTES** — Penetration Testing Execution Standard
- **OSSTMM v3** — Open Source Security Testing Methodology Manual
- **CBEST / TIBER-EU** — for financial sector engagements

---

## Usage

```bash
git clone https://github.com/yourhandle/rtfm.git
cd rtfm
open redteam-field-manual.html   # macOS
xdg-open redteam-field-manual.html  # Linux
start redteam-field-manual.html  # Windows
```

No server required. Works entirely offline. Bookmark phases you use most. The sticky nav lets you jump between sections instantly.

---

## Tool Coverage

Every tool listed is categorized as **FREE**, **PAID**, or **FREE/PAID** (community + commercial tiers). Coverage includes:

**C2 Frameworks** — Cobalt Strike, Havoc, Sliver, Mythic, Brute Ratel C4, Covenant, PoshC2  
**Recon** — Amass, subfinder, httpx, nuclei, Shodan, Censys, SpiderFoot, Maltego  
**AD Attacks** — BloodHound, Rubeus, Certipy, Impacket, PowerView, NetExec  
**Credential Access** — Mimikatz, Nanodump, SharpDPAPI, Hashcat, Rubeus  
**Evasion** — SysWhispers3, ThreatCheck, ScareCrow, Freeze, UACME  
**Phishing** — Evilginx 3, GoPhish, Modlishka, King Phisher  
**Lateral Movement** — Impacket suite, Evil-WinRM, NetExec, SCShell  
**Reporting** — Ghostwriter, Pwndoc, PlexTrac, ATT&CK Navigator, Vectr  

---

## Who this is for

- Red teamers preparing for or running full adversary simulation engagements
- Pentesters leveling up from box-by-box assessments to operation-level thinking
- Security engineers who want to understand what red teams actually do
- Students pursuing CRTO, CRTE, OSCP, CRTO II, or similar practical certifications

This is not an introduction to hacking. Basic familiarity with Windows internals, Active Directory, and networking is assumed throughout.

---

## Legal

Everything in this repository is for **authorized security testing only**.

All techniques require written authorization from the system owner before use. A signed Rules of Engagement document is the absolute minimum. Unauthorized access to computer systems is a criminal offense under the Computer Fraud and Abuse Act (US), Computer Misuse Act (UK), and equivalent legislation in virtually every jurisdiction.

The authors accept no liability for misuse. If you need to read this disclaimer twice, this tool is not for you.

---

## Contributing

PRs welcome for:
- New tools that belong in the framework (with free/paid classification)
- Updated TTP IDs following MITRE ATT&CK version releases
- Additional A–Z entries for missing concepts
- Corrections to technique descriptions or tool categories
- Translation to other languages

Please don't open issues asking for help with unauthorized access.

---

## Resources

| Resource | URL |
|----------|-----|
| MITRE ATT&CK | attack.mitre.org |
| ATT&CK Navigator | attack.mitre.org/resources/navigator |
| C2 Matrix | thec2matrix.com |
| IRED.team | ired.team |
| HackTricks | book.hacktricks.xyz |
| RedTeam.guide | redteam.guide |
| LOLBAS | lolbas-project.github.io |
| GTFOBins | gtfobins.github.io |
| CISA KEV | cisa.gov/known-exploited-vulnerabilities |

---

## License

MIT License — do whatever you want with it, just don't remove attribution and don't use it to break into systems you don't own.

---

*Built for operators. Not for resale. Not for the hands of people who don't understand what they're doing.*
