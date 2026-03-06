# Red Team Field Manual (RTFM)
### Operator Edition — 2025 v6.0 Final

> **AUTHORIZED PERSONNEL ONLY — SIGNED ROE REQUIRED**
>
> All techniques described in this document require **explicit written authorization** from the system owner before use. Unauthorized access to computer systems is a criminal offense under the CFAA, Computer Misuse Act, and equivalent statutes in virtually every jurisdiction globally.

---

**Framework Alignment:** MITRE ATT&CK v15 · PTES · OSSTMM v3 · CBEST · TIBER-EU · NIST SP 800-115  
**Phases:** 16 Total · **Techniques:** 200+ · **Coverage:** A–Z Encyclopedia

---

## Table of Contents

- [Operator Preface](#operator-preface)
- [Phase 00 — Planning & Scoping](#phase-00--planning--scoping)
- [Phase 01 — Reconnaissance & OSINT](#phase-01--reconnaissance--osint)
- [Phase 02 — Weaponization](#phase-02--weaponization)
- [Phase 03 — Initial Access](#phase-03--initial-access)
- [Phase 04 — Execution](#phase-04--execution)
- [Phase 05 — Persistence](#phase-05--persistence)
- [Phase 06 — Privilege Escalation](#phase-06--privilege-escalation)
- [Phase 07 — Defense Evasion](#phase-07--defense-evasion)
- [Phase 08 — Credential Access](#phase-08--credential-access)
- [Phase 09 — Internal Discovery](#phase-09--internal-discovery)
- [Phase 10 — Lateral Movement](#phase-10--lateral-movement)
- [Phase 11 — Collection](#phase-11--collection)
- [Phase 12 — Command & Control](#phase-12--command--control)
- [Phase 13 — Exfiltration](#phase-13--exfiltration)
- [Phase 14 — Impact Simulation](#phase-14--impact-simulation)
- [Phase 15 — Reporting & Debrief](#phase-15--reporting--debrief)
- [A–Z Operator Encyclopedia](#az-operator-encyclopedia)
- [Reference Resources](#reference-resources)

---

## Operator Preface

This is not a certification study guide. It is not a beginner tutorial. It is the operational reference that elite red teamers actually use — stripped of marketing fluff, written the way operators talk to each other in debrief rooms at 2 AM. Every phase maps to real-world engagements. Every tool has been run in production. Every technique has been tested against mature, instrumented, EDR-heavy targets — not lab VMs.

### Key Statistics

| Metric | Value |
|--------|-------|
| Time allocation — Recon & Planning | **40%** |
| Operational phases | **16** |
| Average DA path in unpatched AD | **< 10 minutes** |
| Orgs with ADCS ESC misconfiguration | **90%+** |

### Who Built This Framework

The techniques here come from APT-emulation work against Fortune 500 financial institutions, DoD contractor networks, critical infrastructure operators, and cloud-native SaaS environments. It reflects what works in 2025 against mature, instrumented targets — CrowdStrike Falcon-heavy, MDE-monitored, SIEM-connected environments.

### How to Use This Framework

The 16 phases are **sequential but non-linear**. Real engagements loop. You'll be in Phase 10 (Lateral Movement) and suddenly need to return to Phase 01 because you discovered a new subnet. Use this as a decision tree, not a checklist.

### The 80/20 Rule of Red Teaming

80% of impact comes from reconnaissance, planning, and reporting. 20% is the actual attack. Elite operators have internalized this. Amateur operators rush to Metasploit and wonder why they get caught. The senior operator who spends three days on OSINT before touching anything arrives at exploitation with surgical precision and leaves no trace.

> **Operator Note:** The number one mistake junior operators make is rushing to exploitation. Senior operators spend 40% of engagement time in recon and planning. The more you know before you touch a system, the quieter and more precise your operation. Quiet operations don't get caught. Noisy operations end with a phone call from the CISO stopping the engagement at 9 AM Monday.

> **Operator Note:** Threat actor fidelity is everything in APT emulation. If your client faces APT29, your engagement should use APT29's actual TTPs — not generic pentest techniques. Research their phishing lures, their C2 protocols, their preferred escalation paths, their dwell-time patterns. A red team that accurately emulates APT29 teaches the blue team what APT29 looks like on their wire.

### Unified Kill Chain — All 16 Phases

```
[00 Planning] → [01 OSINT] → [02 Weaponize] → [03 Initial Access] → [04 Execution]
→ [05 Persist] → [06 PrivEsc] → [07 Evasion] → [08 Cred Access] → [09 Discovery]
→ [10 Lateral] → [11 Collection] → [12 C2] → [13 Exfil] → [14 Impact] → [15 Report]
```

### Phase Overview Table

| Phase | Name | MITRE Tactic | Primary Objective | Typical Time | Effort |
|-------|------|-------------|-------------------|--------------|--------|
| 00 | Planning & Scoping | PRE-ATT&CK | Legal auth, threat profile, infra, ROE | 15–25% | High |
| 01 | Reconnaissance | TA0043 | Attack surface mapping, target intel | 10–20% | High |
| 02 | Weaponization | TA0042 | Custom payloads, phishing infra, C2 setup | 5–15% | Very High |
| 03 | Initial Access | TA0001 | First foothold in target environment | 5–10% | High |
| 04 | Execution | TA0002 | Run attacker code on target systems | Ongoing | High |
| 05 | Persistence | TA0003 | Survive reboots, maintain access | Ongoing | Medium |
| 06 | Privilege Escalation | TA0004 | Elevate to SYSTEM / Domain Admin | 10–20% | Very High |
| 07 | Defense Evasion | TA0005 | Avoid detection throughout engagement | Ongoing | Very High |
| 08 | Credential Access | TA0006 | Harvest hashes, tickets, plaintext creds | 5–10% | High |
| 09 | Discovery | TA0007 | Internal recon: hosts, users, data | 5–10% | Medium |
| 10 | Lateral Movement | TA0008 | Traverse network toward objective | 10–20% | High |
| 11 | Collection | TA0009 | Identify and stage target data | 5–10% | Medium |
| 12 | Command & Control | TA0011 | Maintain operator ↔ implant comms | Ongoing | Very High |
| 13 | Exfiltration | TA0010 | Move data out without triggering DLP | 2–5% | High |
| 14 | Impact Simulation | TA0040 | Demonstrate business impact potential | 2–5% | Medium |
| 15 | Reporting & Debrief | POST-ENG | Deliverable that drives real remediation | 15–25% | Very High |

---

## Phase 00 — Planning & Scoping

> **PRE-ATT&CK · Time: 15–25% · Legal Risk: Critical**

The phase that separates professionals from amateurs. Poor scoping creates legal liability. Poor threat profiling produces irrelevant findings. Elite operators spend a fifth of engagement time here — and it pays off in every phase that follows.

**Tags:** ROE Required · GOJL Required · Threat Profile · Infra Setup · Deconfliction · Data Handling

---

### Legal Foundation — Non-Negotiable

| Document | What Must Be In It |
|----------|-------------------|
| **Rules of Engagement** | In-scope IP ranges written explicitly. Excluded systems (production DB, SCADA, OT networks, HA clusters). Active hours and maintenance windows. Emergency stop procedure with direct phone numbers. Who can authorize scope expansion mid-engagement. |
| **Statement of Work** | Deliverables, timeline, liability cap, payment schedule, NDA terms, breach notification responsibility. If you capture real PII, who notifies the regulator? Clarify in writing. |
| **Get-Out-of-Jail Letter** | Signed, wet-ink authorization. Carried physically by every operator deployed. Digital copy on encrypted device. This is the document that keeps operators out of handcuffs when local police respond to an alarm at 3 AM. |
| **Data Handling Agreement** | How captured PII, credentials, screenshots are stored (AES-256 encrypted vault), transmitted (Signal/encrypted email only), retained (delete within X days of report delivery), and destroyed (documented destruction with hash verification). |
| **Deconfliction Protocol** | SOC notification procedure. Indicators your traffic will generate. Traffic source IP ranges. Agreed timestamps for deconfliction check-ins. Without this, you risk the real SOC detecting you and triggering a real incident response that burns the engagement. |
| **Third-Party Cloud Addendum** | AWS, Azure, GCP have their own penetration testing policies. You must notify them in advance for certain test types. Not doing so can result in account termination or legal action from the cloud provider — not just the target. |

---

### Engagement Type Selection

| Type | Description | When to Use |
|------|-------------|-------------|
| **Black Box** | Zero prior knowledge. Most realistic. Slowest. | First-time engagements, realistic threat simulation |
| **Grey Box** | Some info: network ranges, one low-priv account. | Time-constrained engagements, assumed-breach validation |
| **Crystal Box** | Full architecture docs, source code access. | Code review + runtime exploit validation |
| **Assumed Breach** | Start with foothold already established. | Validate post-compromise detection and response |
| **Full APT Sim** | Multi-month, target-specific threat actor emulation. | Mature security programs, TIBER-EU/CBEST compliance |
| **Purple Team** | Collaborative: RT executes, BT watches in real-time. | Detection engineering, tuning detection rules |

---

### Attack Infrastructure Setup

> **Critical:** Set up infrastructure **weeks before engagement start.** Domains aged <30 days get flagged by reputation engines. C2 servers registered days before use have no reputation history. Rushed infra = burned infra = terminated engagement.

| Component | Setup Notes & Elite Practices |
|-----------|------------------------------|
| **Domains** | Buy 60+ days before use. Age with legitimate-looking web traffic. Register in the same industry vertical as the target. Use separate registrar for each campaign. Never reuse domains across clients. |
| **Redirectors** | VPS running Nginx with mod_rewrite. Profile-aware: valid C2 callbacks → real C2 server. All other traffic (SOC investigations) → legitimate website. Use Cloudflare Workers as an additional layer. |
| **C2 Servers** | Never exposed directly. Always behind redirectors. Different geographic jurisdiction from redirectors. Firewall accepts only redirector IPs. Wipe completely between engagements. Use dedicated VPS with full disk encryption. |
| **SMTP / Phishing Infra** | Full SPF, DKIM, DMARC on every sending domain. Warm up with legit mail traffic for 2 weeks before campaign. Test deliverability with mail-tester.com (score 10/10 before using). Separate sending domain from C2 domain. |
| **Operator OPSEC** | Dedicated VMs per campaign (fresh Kali/ParrotOS). No personal accounts, no personal credentials anywhere. VPN to redirector before all C2 access. Signal or Matrix for team comms. Encrypted note-taking with Cryptpad. |
| **Logging** | Log every action with timestamps from day one. Mandatory for report accuracy. Use Ghostwriter's operation log or a timestamped markdown file at minimum. Screenshot every significant finding immediately. |

---

### Threat Actor Profiling

Define the adversary **before** you run a single scan. Consult MITRE ATT&CK Groups, CISA advisories, Mandiant APT reports, and industry ISAC threat bulletins for your client's sector.

| Profile Element | Details |
|----------------|---------|
| **Actor Group** | Identify 1–2 primary threat actors targeting the client's industry. e.g., Financial sector → FIN7, Scattered Spider. Defense → APT10, APT41. Healthcare → FIN12, Lazarus Group. |
| **TTPs** | Pull the actor's MITRE ATT&CK profile. Map their top initial access, execution, persistence, and C2 techniques. Your engagement TTPs should match. |
| **Crown Jewels** | Define success before starting. DA certificate, specific database, wire transfer system, classified document. Ambiguous objectives produce watered-down reports. |
| **Success Metrics** | Dwell time achieved, objectives reached, MTTD (mean time to detect), MTTR (mean time to respond), number of detection gaps demonstrated, critical paths found. |

---

## Phase 01 — Reconnaissance & OSINT

> **TA0043 · Time: 10–20% · Risk: Passive**

Know the target better than their own IT team — without ever touching their network. Elite operators spend real time here. Every hour in recon saves three hours in exploitation and prevents one costly mistake.

---

### Passive Reconnaissance Techniques

| ATT&CK ID | Technique | What You're Looking For |
|-----------|-----------|------------------------|
| T1596 | **Technical Databases** | Shodan/Censys/FOFA for exposed infrastructure. VPNs, OWA, RDP, Jenkins, Citrix, old Exchange, exposed Kubernetes dashboards — every one is an initial access candidate. |
| T1593 | **Certificate Transparency** | crt.sh reveals every subdomain ever issued a certificate. Historical WHOIS for IP range history. Wayback Machine for old endpoints, deprecated APIs, exposed config files. |
| T1591 | **Organization Recon** | LinkedIn for org chart: IT directors, sysadmins, security team, Finance. Job postings reveal tech stack. Glassdoor reviews mention specific tools in complaints. |
| T1589 | **Identity Intelligence** | Email pattern discovery (first.last@, flast@). Dehashed/LeakIX for breached credentials. Have employees appeared in prior breaches? Password reuse is endemic. Credential stuffing against VPN or O365 with breach data is 30%+ effective. |
| T1594 | **Code Repository Mining** | GitHub Advanced Search: `org:companyname password / api_key / SECRET / .env / BEGIN RSA`. TruffleHog against public repos with entire git history. Recent intern projects are goldmines. |
| T1596.005 | **Cloud Storage** | S3 bucket permutation bruteforce. Open Azure blob containers. Public GCS buckets. S3Scanner automates. Companies leave more exposed here than anywhere else. |
| — | **DNS Enumeration** | Fierce, DNSrecon, dnsX for comprehensive DNS mapping. Zone transfers on misconfigured auth DNS. ASN enumeration via BGP data (RIPE/ARIN) for complete IP range picture. |

---

### Active Reconnaissance Techniques

| ATT&CK ID | Technique | Approach & Notes |
|-----------|-----------|-----------------|
| T1595 | **Active Scanning** | Subdomain enum → live host detection → port scan → service fingerprint → vuln scan. Run from your own infrastructure — never your home IP or client network. |
| T1595.002 | **Vulnerability Scanning** | Nuclei with CVE + misconfig templates against all discovered services. Focus on public-facing: VPN appliances, mail servers, web apps. Manually verify any critical findings. |
| T1590 | **Network Topology** | ASN enumeration for all owned IP ranges. Reverse DNS for subnet naming conventions. WHOIS history for previously-owned ranges. Internal topology inference from SSL cert SANs. |
| — | **Web App Fingerprinting** | Whatweb, Wappalyzer, builtwith for tech stack. HTTP headers reveal framework versions. Eyewitness screenshots all web services for visual triage. |

> **Operator Note:** Chain tools systematically: `subfinder → httpx → nuclei → EyeWitness`. End result is a visual gallery of every web-facing service with tech fingerprinting and known CVEs. Then manually review for low-hanging fruit: default creds on admin panels, unauthenticated APIs, exposed `.git` directories, Swagger/OpenAPI docs that expose all endpoints, and version disclosure in headers.

---

### OSINT Workflow — Recommended Sequence

```bash
# Phase 1 OSINT Workflow — Run in order

# 1. Subdomain enumeration
$ subfinder -d target.com -all -o subs.txt
$ amass enum -passive -d target.com >> subs.txt

# 2. Resolve live hosts
$ cat subs.txt | dnsx -silent -a -resp | tee live_hosts.txt
$ cat subs.txt | httpx -status-code -title -tech-detect -o web_services.txt

# 3. Visual recon
$ eyewitness --web -f web_services.txt --no-prompt -d screenshots/

# 4. Vulnerability scanning
$ nuclei -l web_services.txt -t cves/ -t misconfiguration/ -o nuclei_hits.txt

# 5. Secret hunting
$ trufflehog github --org=TargetOrg --only-verified
$ gitleaks detect --source=/tmp/cloned-repos/ -v
```

---

### OSINT & Recon Toolchain

| Tool | Description | Cost |
|------|-------------|------|
| Amass | Best subdomain enumeration, OWASP project, 50+ data sources | Free |
| subfinder | Fast passive subdomain enumeration, ProjectDiscovery | Free |
| httpx | Live host probing, tech detection, screenshot integration | Free |
| nuclei | 5000+ CVE and misconfig templates, fastest scanner available | Free |
| Shodan | Internet device scanner — API essential for professional use | Free/Paid |
| Censys | Certificate intelligence + service fingerprinting | Free/Paid |
| FOFA | China-based internet scanner, finds assets missed by Shodan | Free/Paid |
| theHarvester | Email, hostname, URL, employee name harvesting | Free |
| truffleHog | Deep git history secret scanning with verified results | Free |
| gitleaks | Fast secrets detection in git repositories and CI/CD | Free |
| SpiderFoot | Automated OSINT framework, 200+ modules | Free |
| Maltego | Visual OSINT link analysis, relationship mapping | Free/Paid |
| Dehashed | Leaked credential database search — essential subscription | Paid ($15/mo) |
| LeakIX | Exposed services and leaked data intelligence | Free/Paid |
| S3Scanner | S3 bucket enumeration and misconfiguration detection | Free |
| EyeWitness | Web service screenshotting and report generation | Free |

---

## Phase 02 — Weaponization

> **TA0042 · Time: 5–15% · Technical Depth: Maximum**

Building tools, payloads, and infrastructure. Default tools get caught within seconds by mature EDR. Custom tooling with unique signatures, behavioral evasion, and environment-aware staging is what gets you through day one.

---

### Payload Development Techniques

| Technique | Elite Operator Approach |
|-----------|------------------------|
| **Custom Loaders** | Write shellcode loaders in Nim, Rust, or Go. New languages have no existing AV signatures. AES-256 encrypt shellcode blob, store key separately or derive at runtime. Compile with different flags per engagement to change PE hash. |
| **Multi-Stage Delivery** | Stage 1: tiny dropper that validates environment (domain joined? specific hostname? not a VM? user activity present?). Only then fetches Stage 2 from C2. Prevents detonation in automated sandbox analysis. |
| **Anti-Analysis** | Sleep 180s on first run (most sandboxes timeout at 120s or fast-forward). Check mouse movement delta. Check disk size (>50GB). Check hostname against known sandbox names. Check for running analysis processes. All checks fail-closed: if suspicious, exit cleanly. |
| **String Obfuscation** | XOR/RC4 all IOC strings at compile time — tool names, IPs, registry keys, API names. Decrypt at runtime only when needed. Stack strings rather than storing as literals. Zero static signatures. |
| **BOF Development** | Beacon Object Files execute inside beacon memory. No new process created. Harder to detect than fork-and-run. Write custom BOFs for common post-ex actions rather than executing standalone tools that spawn suspicious processes. |
| **PE Stomping** | Load a legitimate PE into memory, overwrite its code section with shellcode, redirect execution. The on-disk file remains legitimate. Memory shows a valid PE image. Signature-based detection finds nothing suspicious on disk. |
| **Module Stomping** | Load a legitimate DLL, stomp its `.text` section with shellcode. CFG-compliant, module appears legitimate to most tools inspecting loaded modules. More sophisticated than classic process injection. |

---

### Phishing Infrastructure Build

| Component | Setup Details |
|-----------|--------------|
| **Domain Selection** | Typosquatting, combo-squatting, or lookalike domains mimicking vendors the target uses. Check email-reputation services (Sender Score, DNSBL) before using. Register with privacy protection. |
| **Mail Server Setup** | VPS running Postfix/Sendmail. Full SPF, DKIM 2048-bit, DMARC. Check mail-tester.com — achieve 10/10 before first send. Warm up domain for 2 weeks with low-volume legitimate-looking mail. |
| **Evilginx AiTM** | Deploy Evilginx 3 behind a CDN. Use phishlets for O365, Azure, Google Workspace, Okta, Duo. Captures session tokens post-MFA — bypasses TOTP, push notifications, hardware tokens. |
| **HTML Smuggling** | Assemble payload client-side via JS `Blob()` + `URL.createObjectURL()`. Email gateway scans an HTML file — no embedded attachment detected. Browser downloads assembled binary. Combine with ISO/LNK container to bypass Mark-of-the-Web. |
| **Lure Design** | Spearphish wins come from specificity: reference real internal project names found via LinkedIn, real employee names, real vendors from job postings, real events from company news. Generic lures get 2% click rate. Targeted lures get 40%+. |

> **Operator Note:** HTML Smuggling remains the most consistently effective delivery method in 2025. Email gateways scan the HTML file and find no attachment. The JS executes in the browser, assembles the payload from a base64 blob, and triggers a download. Pair with a DocuSign or SharePoint lure template. The user sees a document they were expecting. Execution happens in three clicks.

---

### Weaponization Toolkit

| Tool | Description | Cost |
|------|-------------|------|
| Cobalt Strike | Industry-standard C2 — customize profiles, write BOFs | Paid ($3.5k/yr) |
| Havoc C2 | Best free CS alternative, modern evasion, active development | Free |
| Sliver | Go-based C2, mTLS/DNS/HTTP/WireGuard transports | Free |
| Brute Ratel C4 | EDR-aware commercial C2, userland hooks awareness built-in | Paid ($2.5k/yr) |
| Donut | Converts PE/.NET/VBScript to position-independent shellcode | Free |
| ScareCrow | Shellcode loader generator with multiple EDR bypass modes | Free |
| Freeze | Shellcode loader with ETW/AMSI patches, PE cloaking built-in | Free |
| ThreatCheck | Find exact bytes triggering Defender/AMSI signatures | Free |
| Evilginx 3 | AiTM proxy — captures MFA session tokens post-authentication | Free |
| GoPhish | Phishing campaign management, tracking, templating | Free |
| NimPackt | Nim-based shellcode packer with multiple injection techniques | Free |
| PEzor | Open-source PE packer with antidebug, antisandbox | Free |

---

## Phase 03 — Initial Access

> **TA0001 · Time: 5–10% · Creativity: Maximum**

Getting the first foothold. The most creative phase. Combine technical exploitation, human psychology, supply chain compromise, and physical access depending on scope. The entry point determines everything that follows.

---

### Human-Based Vectors

| ATT&CK ID | Technique | 2025 Effectiveness & Notes |
|-----------|-----------|---------------------------|
| T1566.001 | **Spearphish Attachment** | ISO/LNK combos, OneNote embeds, SVG smuggling, HTML blobs. Office macros effectively dead without GPO changes. Always test deliverability before launching campaign. |
| T1566.002 | **Spearphish Link** | Evilginx AiTM captures session tokens — bypasses every MFA type. QR code phishing (Quishing) defeats URL scanning engines on email gateways. QR codes resolved on mobile browsers with weaker security posture. |
| T1598 | **Vishing** | Call helpdesk impersonating employee. Request password reset or MFA bypass. Reference real internal details from OSINT (manager name, project name, HR system). Effective ~60% of the time in most enterprise environments. |
| T1566.004 | **MFA Fatigue** | Spray until valid creds. Send 30+ push notifications in rapid succession. Call target pretending to be IT: "There's unusual activity on your account — approve the next notification to verify your identity." Bypass rate: ~60%. |
| — | **Callback Phishing** | Send email with fake invoice or security alert containing only a phone number. Target calls the number. Social engineer over the phone: "I'll need to install a remote support tool." TeamViewer/AnyDesk → initial access. No malicious URL, no attachment. |

---

### Technical Exploitation Vectors

| ATT&CK ID | Technique | Priority Targets |
|-----------|-----------|-----------------|
| T1190 | **Public App Exploit** | VPN appliances (Fortinet CVE-2023-27997, Citrix Bleed, Pulse Secure), Exchange (ProxyShell, ProxyNotShell), exposed admin panels with default creds. Check CISA KEV list weekly. |
| T1133 | **External Remote Services** | VPN/RDP with leaked/sprayed credentials. Unpatched CVEs on Citrix Netscaler, Fortinet, Pulse Secure. `Shodan dork: port:3389 org:"TargetOrg"` |
| T1078 | **Valid Account Spray** | Slow distributed password spray against O365/Azure AD. 1 attempt per account per 60 minutes avoids lockouts. Use FireProx for IP rotation. TREVORspray has built-in lockout avoidance. Common passwords: `Summer2024!`, `CompanyName1!`, `Welcome123` |
| T1195 | **Supply Chain** | Compromise a trusted MSP, software vendor, or CI/CD pipeline. One MSP foothold = access to dozens of clients. High complexity, very high reward. |
| T1199 | **Trusted Relationship** | Compromise a vendor or partner with trusted network access to the target. IT support, law firms, accounting firms, managed security providers — all commonly have direct VPN or firewall-permitted access. |

> **Operator Note:** Password spraying works in 2025 at most enterprises. The passwords still in active use: `Summer2024!`, `Welcome1`, `CompanyName1!`, `Passw0rd!`, `[Month][Year]!`. Run these exact patterns first before any wordlist. Go slow: 1 attempt per username per 60 minutes. Use TREVORspray or FireProx for IP rotation.

---

### Initial Access Toolkit

| Tool | Description | Cost |
|------|-------------|------|
| Evilginx 3 | AiTM phishing proxy, session token capture, bypasses all MFA | Free |
| TREVORspray | O365 password spray with built-in lockout avoidance | Free |
| FireProx | AWS API GW proxy for spraying — rotating IPs automatically | Free |
| Burp Suite Pro | Web application testing — indispensable for web app exploitation | Paid ($499/yr) |
| nuclei | CVE templates for VPN/OWA/WebApp exploitation chain | Free |
| MFASweep | Enumerate Microsoft MFA coverage gaps across all endpoints | Free |
| SQLmap | Automated SQL injection detection and exploitation | Free |
| Flipper Zero | RFID/NFC/Sub-GHz/IR for physical social engineering | Paid (~$200) |
| WiFi Pineapple | Rogue AP for physical Wi-Fi attacks and credential capture | Paid (~$120) |
| o365spray | O365 user enum and password spraying framework | Free |

---

## Phase 04 — Execution

> **TA0002 · Ongoing Phase · EDR Risk: Critical**

Running your code on target systems. The EDR detection problem lives here. Every technique below minimizes process creation, avoids suspicious parent-child relationships, and leverages behavioral blind spots in modern endpoint detection.

---

### Execution Techniques

| ATT&CK ID | Technique | Operator Notes |
|-----------|-----------|----------------|
| T1059.001 | **PowerShell** | Patch AMSI in memory first using direct syscalls. Use encoded/compressed commands. Never call `powershell.exe` from your implant — invoke via reflective loader or BOF to avoid parent-child process anomalies. |
| T1218 | **Signed Binary Proxy** | LOLBAS: rundll32, regsvr32, mshta, msiexec, certutil, wmic, wscript, cscript. All signed by Microsoft, present by default. Check LOLBAS.github.io for current bypass status. |
| T1055 | **Process Injection** | Inject into explorer.exe, svchost.exe, Teams.exe, OneDrive.exe. Classic CreateRemoteThread, process hollowing, thread hijacking, APC injection. Always use direct syscalls (SysWhispers3) to avoid NTDLL user-mode hooks. |
| T1055.004 | **APC Injection** | Queue APC to an alertable thread in a target process. Less monitored than CreateRemoteThread in most EDR implementations. Requires finding a thread in alertable wait state — use `NtTestAlert` trick. |
| T1620 | **Reflective Code Load** | Load .NET assemblies via execute-assembly BOF (in-memory, no disk write). Reflective DLL injection for unmanaged code. Everything stays in beacon memory. |
| T1059.003 | **Windows Command Shell** | When you must use cmd.exe: spawn it from a legitimate parent (explorer, svchost), use indirect execution via WMI or Task Scheduler rather than direct CreateProcess from your beacon. |

> **Operator Note — The Cardinal Rule:** Never create a new process from your implant parent. Spawning `cmd.exe`, `powershell.exe`, or any executable directly from beacon is detected by behavioral analytics in seconds. Use BOFs (execute in beacon memory), inject into already-running legitimate processes, or use indirect execution paths. SysWhispers3 for direct syscalls bypasses all user-mode NTDLL hooks.

---

### Execution Evasion Stack

| Layer | Technique | What It Defeats |
|-------|-----------|----------------|
| Layer 1 | AMSI patch in memory | PowerShell/script scanning |
| Layer 2 | ETW provider patch | Event Trace for Windows logging |
| Layer 3 | Direct syscalls (SysWhispers3) | NTDLL API hook inspection |
| Layer 4 | Process injection vs. spawn | Parent-child behavioral detection |
| Layer 5 | Encrypted in-memory beacon | Memory scanning for shellcode |
| Layer 6 | Sleep + jitter (45–90 min) | Beaconing pattern detection |

---

### Execution Toolkit

| Tool | Description | Cost |
|------|-------------|------|
| SysWhispers3 | Direct syscall stub generator — bypasses all NTDLL hooks | Free |
| BOF Collection | Beacon Object Files library for CS/Havoc | Free |
| LOLBAS | Living-off-the-land binary database, updated continuously | Free |
| SharpCollection | Compiled .NET offensive tooling for in-memory execution | Free |

---

## Phase 05 — Persistence

> **TA0003 · Ongoing Phase · Stealth: Critical**

Surviving reboots, credential resets, and periodic security reviews without triggering anomaly detection. The best persistence mechanisms look indistinguishable from legitimate system components to anyone casually inspecting the environment.

---

### Windows Persistence Techniques

| ATT&CK ID | Method | Stealth | Notes |
|-----------|--------|---------|-------|
| T1546.003 | **WMI Subscriptions** | ◆ STEALTHY | Permanent WMI event subscriptions survive all reboots. No new service, no obvious registry keys. Only visible via WMI query. Ideal for high-value, long-term access. |
| T1053.005 | **Scheduled Tasks** | ◆ LOW-MED | Name task identically to a legitimate Windows task. Set trigger: ONLOGON, specific time, or system event. XML-based — writable directly if you know the format. |
| T1547.001 | **Registry Run Keys** | ◆ MEDIUM | `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` requires no admin. Noisier than WMI. Use only when quick persistence is needed. |
| T1543.003 | **Windows Service** | ◆ MEDIUM | Requires admin. Name it close to existing services. EventID 7045 logs service creation — notable in mature SIEMs. Set recovery actions to auto-restart. |
| T1546.007 | **Netsh Helper DLL** | ◆ LOW | Register a malicious DLL as a netsh helper. Executes whenever `netsh.exe` is called. Rarely monitored. Survives reboots. Requires admin. |
| T1547.014 | **Active Setup** | ◆ LOW | Active Setup registry key runs payload for each user on first login. Per-user persistence without elevated privileges for HKCU variant. Rare enough that most Blue Teams don't monitor it. |

---

### Linux Persistence Techniques

| Method | Stealth | Notes |
|--------|---------|-------|
| **Cron Jobs** | ◆ LOW-MED | User-level: `crontab -e`. Root-level: `/etc/cron.d/` with a boring filename. Callback every 10–15 minutes. Use `curl\|bash` or `wget` pattern for fileless execution. |
| **Systemd Unit** | ◆ LOW | Create `/etc/systemd/system/[servicename].service`. Set `Restart=always`, `RestartSec=60`. `systemctl enable --now`. Looks like legitimate software. Very reliable. |
| **SSH Authorized Keys** | ◆ LOW | Add public key to `~/.ssh/authorized_keys` (root if available). Survives all password changes. Invisibly persistent. Combine with non-standard port if possible. |
| **LD_PRELOAD Hijack** | ◆ STEALTHY | Add malicious shared library to `/etc/ld.so.preload`. Functions intercept calls to any binary. Effectively a kernel-mode hook in userspace. Extremely stealthy. |
| **PAM Module** | ◆ STEALTHY | Install malicious PAM module that captures credentials on every authentication. Requires root. Survives service restarts. Logs all passwords regardless of auth method. |

---

### Cloud Persistence

| Platform | Technique | Notes |
|----------|-----------|-------|
| **Azure** | Create service principal with Contributor role. Add credentials (secret or certificate) to existing app registration. Create new guest user in tenant. | Invisible unless IAM is actively audited. |
| **AWS** | Add IAM user with programmatic access and Admin policy. Create hidden access keys on existing high-privilege user. Lambda function as persistence vehicle. | Modify existing Lambda for backdoor execution. |
| **GCP** | Create service account with Owner role. Generate key file. Add OAuth token to existing service account. Cloud Function as callback mechanism. | |
| **M365** | Add OAuth app with persistent delegated permissions. Register enterprise application. Automated email forwarding rule. | Invisible in Outlook, survives password change. |

---

## Phase 06 — Privilege Escalation

> **TA0004 · Time: 10–20% · Complexity: Maximum**

From low-privilege user to SYSTEM or Domain Admin. The most technically demanding phase. Active Directory environments have an embarrassing number of escalation paths — BloodHound finds most of them automatically.

---

### Windows Local Privilege Escalation

| ATT&CK ID | Technique | Operator Notes |
|-----------|-----------|----------------|
| T1134.001 | **Token Impersonation** | `SeImpersonatePrivilege` on service accounts → SYSTEM via GodPotato (Win10-Win11), EfsPotato, PrintSpoofer, JuicyPotatoNG. Hits on 80%+ of IIS application pool accounts and SQL Server service accounts. First thing to check after initial access. |
| T1574.010 | **Service Binary Abuse** | Writable service binary path, unquoted service path, weak ACLs on service config key. PowerUp.ps1 / WinPEAS enumerate in seconds. Replace binary → restart service → SYSTEM shell. |
| T1548.002 | **UAC Bypass** | fodhelper.exe, eventvwr.exe, CMSTPLUA COM object hijack, WSReset.exe, sdclt.exe. UACME project has 65+ bypass techniques. Most work on fully patched Windows 11. Never trigger UAC prompt — always use auto-elevating bypass. |
| T1611 | **Container Escape** | Privileged container → host root. Mounted Docker socket → create privileged container → mount host FS. Dangerous capabilities (SYS_ADMIN, SYS_PTRACE). `deepce.sh` enumerates all container escape paths automatically. |
| T1068 | **Kernel Exploit** | Check kernel version against known vulnerabilities. PrintNightmare (MS-PAR), HiveNightmare. Use only as last resort — kernel exploits can BSOD systems and are loud in EDR telemetry. |

---

### Active Directory Privilege Escalation

| Technique | Tooling | Notes |
|-----------|---------|-------|
| **Kerberoasting** | Rubeus / GetUserSPNs.py | Any domain user. Request service tickets. Crack hash offline. Filter `adminCount=1` first — high-value targets. Service accounts often have pre-2020 passwords. |
| **AS-REP Roasting** | Rubeus / GetNPUsers.py | No auth required for target accounts with pre-auth disabled. Offline crack RC4 hash. Often forgotten accounts with old passwords and lingering privileged group membership. |
| **ACL / ACE Abuse** | BloodHound + PowerView | GenericAll → reset password. GenericWrite → targeted Kerberoast, shadow credentials, logon script. WriteDACL → grant yourself DCSync rights. WriteOwner → take ownership. |
| **ADCS ESC1–ESC13** | Certipy | Present in 90%+ of environments. ESC1: any user can enroll cert as DA. ESC3: enrollment agent abuse. ESC6: EDITF_ATTRIBUTESUBJECTALTNAME2. ESC8: NTLM relay to AD CS web enrollment. |
| **Shadow Credentials** | Whisker / Certipy | Write Key Credential to target's `msDS-KeyCredentialLink`. Authenticate via PKINIT. Requires write access to target object. Returns TGT and NT hash. No password change, no lockout. |
| **Unconstrained Delegation** | Rubeus + SpoolSample | Get admin on system with unconstrained delegation. Coerce DC authentication via PrinterBug/PetitPotam. DC TGT lands in memory. Extract with Rubeus monitor+extract. DCSync. Full domain in 10 minutes. |
| **Constrained Delegation Abuse** | Rubeus s4u | Use S4U2Self + S4U2Proxy to impersonate any user to the constrained service. Often leads to DA if constrained service is privileged. |

---

### Privilege Escalation Toolkit

| Tool | Description | Cost |
|------|-------------|------|
| BloodHound CE | AD attack path visualization — run immediately on domain access | Free |
| Rubeus | Kerberoast, AS-REP roast, S4U abuse, ticket operations | Free |
| Certipy | ADCS enumeration, ESC1-13 exploitation, shadow credentials | Free |
| GodPotato | SeImpersonate → SYSTEM, works Win10 through Win11 | Free |
| WinPEAS | Automated Windows privilege escalation checker | Free |
| PowerView | AD recon, ACL enumeration, object attribute queries | Free |
| UACME | 65+ UAC bypass techniques, compiled and ready | Free |
| Impacket | Python AD attack suite — DCSync, GetUserSPNs, secretsdump | Free |
| Whisker | Shadow credentials attack via msDS-KeyCredentialLink | Free |
| LinPEAS | Linux privilege escalation checker | Free |

---

## Phase 07 — Defense Evasion

> **TA0005 · Ongoing Discipline · Priority: Maximum**

This is not a phase. It is a constant discipline that overlays every other phase from initial access forward. Every action must be evaluated through an evasion lens before execution. Operators who treat evasion as an afterthought get caught.

---

### Defense Evasion Techniques

| ATT&CK ID | Technique | Implementation Details |
|-----------|-----------|----------------------|
| T1562.006 | **Patch ETW/AMSI** | Patch `AmsiScanBuffer()` and `EtwEventWrite()` in process memory via direct syscalls. Do this at the start of every PowerShell session and every managed execution context. |
| T1055.012 | **Process Hollowing** | Spawn legitimate process (svchost, runtimebroker) in suspended state. Unmap its memory. Map shellcode. Adjust entry point. Resume. Process appears legitimate — correct image path, signed binary name. |
| T1027 | **Compile-Time Obfuscation** | XOR/RC4 all strings at compile time. Encrypt payload configuration. Use LLVM obfuscation passes. Stack strings rather than global literals. Randomize function names per build. Each compiled binary should have a unique hash. |
| T1070.001 | **Selective Log Clearing** | Never wipe entire event log — that is itself an alert (EventID 1102). Target specific EventIDs: 4624 (logon), 4625 (failed logon), 4776 (NTLM auth), 4768 (TGT request), 7045 (service install). Clear only the events you created. |
| T1036 | **Masquerading** | Rename offensive tools to match legitimate process names. Copy PE metadata from real binaries using Resource Hacker. Timestomp to match filesystem timestamps of system binaries. Deploy in paths where that binary is expected. |
| T1497 | **Virtualization Evasion** | Detect sandbox: registry keys (VBOX, VMware artifacts), disk size check (<50GB), uptime check (<10 min), mouse movement check, CPU core count (<4), memory (<4GB). Exit cleanly if sandbox detected. |
| T1553 | **Code Signing** | Self-signed certificates reduce SmartScreen friction. Stolen legitimate code-signing certs are the gold standard. Authenticode signing with cross-signed cert raises trust level significantly. |

---

### EDR Detection Surface by Technique

| Technique | Detection Level |
|-----------|----------------|
| WMI Persistence | 🟢 LOW |
| DCOM Lateral Movement | 🟢 LOW |
| WinRM Lateral Movement | 🟡 MEDIUM |
| Pass-the-Hash | 🟡 MEDIUM |
| Direct LSASS Dump | 🔴 HIGH |
| SMB PsExec | 🔴 HIGH |
| PowerShell Empire (default) | 🔴 CRITICAL |

---

> **Operator Note:** Know your target EDR before running a single payload. CrowdStrike Falcon, SentinelOne Singularity, and Microsoft Defender for Endpoint each have distinct detection logic, kernel callback registration patterns, and behavioral analytics. Research the specific product version. Set up a lab VM with the same product. A payload that bypasses Windows Defender gets caught by CrowdStrike in 400 milliseconds.

> **Operator Note:** Sleep and jitter are massively underutilized. Most operators leave defaults: 60 seconds sleep, 0% jitter. Elite operators running long engagements use 45–90 minute sleep intervals with 30–50% jitter. A beacon calling home once every hour looks like legitimate background traffic. Operate slow. Operate quiet. Operate patient.

> **Operator Note:** Test before you deploy. ThreatCheck identifies the exact bytes in your payload that trigger Windows Defender. DefenderCheck does the same for AMSI. Run your final payload through a sandbox (ANY.RUN, Hybrid Analysis) before use.

---

## Phase 08 — Credential Access

> **TA0006 · Time: 5–10% · Impact: Maximum**

Credentials are the master key in almost every environment. The techniques haven't fundamentally changed — the detection sophistication has. The answer is operational precision: target the right accounts, use the stealthiest available method, process offline.

---

### Credential Access Techniques

| ATT&CK ID | Technique | 2025 Reality & Approach |
|-----------|-----------|------------------------|
| T1003.001 | **LSASS Memory** | Direct dump (ProcDump, Task Manager, comsvcs.dll) caught by every mature EDR. Alternatives: **Nanodump** (custom minidump format), **HandleKatz** (duplicate handle approach, never touches LSASS directly), **PPLBlade** (Protected Process Light bypass), indirect dump via forked process. |
| T1003.003 | **NTDS.dit** | VSS shadow copy → robocopy NTDS.dit and SYSTEM hive → `secretsdump.py` offline. Or DCSync if you have Replication-Get-Changes rights (cleaner, no disk access on DC). Golden ticket from krbtgt hash provides 10-year domain persistence. |
| T1558.003 | **Kerberoasting** | `Rubeus kerberoast /outfile:hashes.txt /pwdsetdate:01/01/2020`. Hashcat `-m 13100` with targeted wordlist: company name variants, season+year combos, rockyou.txt top 10k. Target `adminCount=1` service accounts exclusively. |
| T1550.002 | **Pass-the-Hash** | NTLM hash from LSASS → lateral movement without cracking. `nxc smb 192.168.1.0/24 -u administrator -H [hash] --local-auth`. Generates Type 3 logon events — targeted use, not mass spray. |
| T1555 | **Browser & App Credentials** | SharpChrome for Chrome/Edge saved passwords. SharpDPAPI for Windows Credential Manager, vaults, certificates. KeePass databases — find on file shares, keylog the master password if live. |
| T1552.001 | **Credentials in Files** | Snaffler across all accessible UNC paths: passwords in filenames, `unattend.xml`, `web.config` with connection strings, `.env` files with API keys, PowerShell history files (`ConsoleHost_history.txt`), bash history, SSH private keys. |

> **Operator Note — DCSync:** Requires DS-Replication-Get-Changes and DS-Replication-Get-Changes-All rights. Granted to Domain Admins by default. BloodHound "Users with DCSync Rights" query finds other accounts that have them. Once you have those rights, `secretsdump.py -just-dc` is a one-liner. The resulting krbtgt hash generates Golden Tickets valid for 10 years, surviving all user password resets.

---

### Credential Access Toolkit

| Tool | Description | Cost |
|------|-------------|------|
| Nanodump | Custom LSASS minidump format — bypasses signature detection | Free |
| Mimikatz | The original — use offline on captured dump files now | Free |
| Rubeus | Kerberos ticket operations — Kerberoast, AS-REP, PTT | Free |
| Hashcat | GPU-accelerated hash cracking — 13100 for Kerberoast | Free |
| SharpDPAPI | DPAPI decryption, browser credential extraction | Free |
| NetExec (nxc) | CrackMapExec successor — mass PtH validation | Free |

---

## Phase 09 — Internal Discovery

> **TA0007 · Time: 5–10% · Maps Everything**

Build your complete picture of the internal environment before moving laterally. Rushing lateral movement without discovery is how operators miss the 3-hop path to Domain Admin that was sitting right there.

---

### Discovery Techniques

| ATT&CK ID | Technique | What to Collect & Why |
|-----------|-----------|----------------------|
| T1082 | **System Information** | OS version, exact patch level (check against KEV), AV/EDR product and version, domain-joined status, local admin group members, currently logged-in users. Determines next steps before you do anything else. |
| T1083 | **File Discovery** | Search patterns: `*.kdbx`, `*password*`, `*cred*`, `*vpn*`, `*.pfx`, `*.p12`, `*.rdp`, `*.config`, `unattend.xml`, `web.config`, `*.json` (API keys). Snaffler does this across all accessible shares. |
| T1087 | **Account Discovery** | All domain users, groups, service accounts, admin accounts. Filter `adminCount=1` for privileged accounts. BloodHound/SharpHound collects all of this automatically. AzureHound for Entra ID. |
| T1018 | **Network Discovery** | ARP sweep for local segment. Ping sweep for active hosts. Port scan priority targets (DCs, file servers, SQL servers). Map network segments. Do this from implant memory. |
| T1016 | **Network Configuration** | Routing tables reveal network segments. Internal DNS zones show naming conventions. DHCP server logs show all recent IP assignments. Proxy settings reveal traffic routes. |
| T1135 | **Network Shares** | Enumerate all reachable SMB shares. Check access rights on each. IT shares, HR shares, Finance shares, Backups are highest priority. Snaffler ranks findings by sensitivity automatically. |

> **Operator Note:** Run BloodHound within 10 minutes of getting domain credentials. SharpHound collection: 2–5 minutes. Import ZIP to BloodHound. Run these queries in order: "Shortest Paths to Domain Admins," "Find AS-REP Roastable Users," "Find Kerberoastable Users with High-Value Target," "Users with DCSync Rights," "Shortest Paths to Unconstrained Delegation Systems." In 90% of environments you'll have a clear path within 5 minutes.

---

### BloodHound Cypher Queries

```cypher
-- All Kerberoastable high-value accounts
MATCH (u:User {hasspn:true})-[:MemberOf*1..]->(g:Group)
WHERE g.objectid ENDS WITH '-512' OR g.objectid ENDS WITH '-544'
RETURN u.name, u.pwdlastset

-- Shortest path from owned to Domain Admin
MATCH p=shortestPath((n:User {owned:true})-[*1..]->(m:Group
{name:'DOMAIN ADMINS@DOMAIN.LOCAL'}))
RETURN p

-- Users with DCSync rights
MATCH p=(n)-[:DCSync|AllExtendedRights|GenericAll]->(d:Domain)
RETURN p
```

---

### Discovery Toolkit

| Tool | Description | Cost |
|------|-------------|------|
| BloodHound CE + SharpHound | AD attack path graph — run immediately | Free |
| Snaffler | Sensitive file finder across all accessible shares | Free |
| Seatbelt | Host security posture survey, 100+ checks | Free |
| PowerView | AD enumeration via PowerShell, ACL queries | Free |
| AzureHound | BloodHound data collector for Azure AD / Entra ID | Free |
| CloudFox | Multi-cloud attack surface enumeration (AWS/Azure/GCP) | Free |

---

## Phase 10 — Lateral Movement

> **TA0008 · Time: 10–20% · Noise Risk: High**

Traversing the network with purpose. Every hop must be deliberate and planned with a clear operational reason. Noisy lateral movement triggers behavioral analytics within minutes. Precision over volume.

---

### Lateral Movement Techniques

| ATT&CK ID | Technique | Detection Profile | Notes |
|-----------|-----------|------------------|-------|
| T1550.002 | **Pass-the-Hash** | 🟡 MEDIUM | Creates Type 3 NTLM logon events. Targeted use only — mass PtH across 50 hosts triggers pattern detection immediately. |
| T1021.006 | **WinRM** | 🟢 LOW-MED | Evil-WinRM with stolen creds. Port 5985/5986. Creates WinRM-specific logon events but less correlated than SMB in most SIEM rules. |
| T1021.003 | **DCOM** | 🟢 LOW | MMC20.Application and ShellWindows objects. Lateral execution via COM. Very few organizations have DCOM execution event monitoring. Underdetected. |
| T1021.002 | **SMB Admin Shares** | 🔴 HIGH | EventID 7045 in mature envs. Avoid PsExec in any SOC-monitored environment. wmiexec is almost always the better choice. |
| T1550.003 | **Pass-the-Ticket** | 🟢 LOW-MED | Import forged Kerberos TGT via Rubeus ptt. Zero NTLM — bypasses all PtH detection. Requires valid TGT. |
| T1563 | **RDP Session Hijack** | 🟡 MEDIUM | Hijack disconnected RDP sessions on a system you have SYSTEM on. `tscon.exe [session_id] /dest:[target_session]`. Logs show legitimate user activity. |

---

### Lateral Movement Commands

```bash
# Lateral movement via WMI (quiet)
$ impacket-wmiexec domain.local/svc_backup:Password1@DC01

# Pass-the-Hash via NetExec validation
$ nxc smb 10.10.10.0/24 -u Administrator -H [NTLM_HASH] --local-auth

# Pass-the-Ticket (no NTLM at all)
beacon> rubeus ptt /ticket:[base64_ticket]

# DCOM lateral movement (stealthiest)
beacon> dcomexec target-host C:\Windows\System32\calc.exe
```

> **Operator Note:** `wmiexec.py` is your daily driver for targeted lateral movement when stealth matters. It uses WMI (always-present, legitimate service), writes nothing to disk, and creates minimal log events. For sustained operations: P2P beacons over SMB named pipes — spin up a beacon on the target that phones home to your already-compromised internal system rather than directly to external C2. Keeps external beacon count at 1.

---

### Lateral Movement Toolkit

| Tool | Description | Cost |
|------|-------------|------|
| Impacket Suite | wmiexec, smbexec, atexec, dcomexec, psexec | Free |
| Evil-WinRM | WinRM shell with file upload/download and bypass options | Free |
| NetExec (nxc) | CME successor — mass validation, module library | Free |
| Ligolo-ng | Full layer-3 tunneling through implants, zero dependencies | Free |
| Chisel | HTTP-based TCP tunneling | Free |
| frp | Fast reverse proxy for network pivoting | Free |

---

## Phase 11 — Collection

> **TA0009 · Time: 5–10%**

---

### Collection Targets & Methods

| Target | Method & Notes |
|--------|----------------|
| **SharePoint / Confluence** | Search "password" "VPN" "AWS" "production" "credentials" "API key" "secret". These hit on every engagement — internal wikis are credential graveyards. |
| **Exchange / O365 Mail** | MailSniper or Graph API via TokenTactics. Target C-suite and IT mailboxes. Add silent forwarding rules as persistence. |
| **Network File Shares** | Snaffler across all accessible UNC paths. Prioritize: IT shares (configs, scripts), HR (org charts), Finance (bank info, invoices). Stage and compress before exfil. |
| **Cloud Storage** | GraphRunner for M365 OneDrive/SharePoint. AWS S3 with stolen IAM keys. Azure Storage with compromised service principal. |
| **Database Access** | After reaching SQL server with DA: enumerate all databases, dump high-value tables (users, customers, financial). Capture connection strings from web.config. Avoid `SELECT *` on multi-million row tables — use `TOP 1000` for evidence. |

---

## Phase 12 — Command & Control

> **TA0011 · Ongoing Phase**

---

### C2 Architecture Best Practices

| Component | Elite Setup |
|-----------|-------------|
| **Protocol** | HTTPS primary (443 — always open). DNS fallback (53 — passes almost every egress filter). Both through redirectors. CDN layer optional for additional IP rotation. |
| **Malleable Profile** | Mimic Microsoft Graph API, Slack, Dropbox, OneDrive API traffic exactly. Match timing, packet sizes, URI structure, response format, HTTP headers. Blue team can't block it without blocking the real service. |
| **Redundancy** | Two independent C2 channels. If HTTPS blocked, DNS activates automatically. P2P SMB beacons for network segments with no internet egress. Backup domain ready to activate if primary burned. |
| **Redirectors** | Nginx with profile-aware routing: correct URI + correct User-Agent → proxy_pass to real C2. Everything else → redirect to legitimate website. Cloudflare Workers as additional layer. |

### Nginx Redirector Configuration

```nginx
# Profile-aware redirector — forward only valid C2 callbacks
location /api/v2/update {
    if ($http_user_agent != "Mozilla/5.0 (Windows NT 10.0; Win64; x64)...") {
        return 301 https://www.targetindustry-news.com;
    }
    proxy_pass https://[C2_SERVER_IP]:443;
    proxy_set_header Host $host;
}

# Everything else → benign site
location / {
    return 301 https://www.legitimate-site.com;
}
```

### C2 Frameworks Comparison

| Tool | Strengths | Cost |
|------|-----------|------|
| Cobalt Strike | Industry standard, malleable profiles, mature BOF ecosystem | Paid ($3.5k/yr) |
| Havoc C2 | Best free alternative, modern evasion, active development | Free |
| Sliver | Go-based, mTLS/DNS/HTTP/WireGuard, open source | Free |
| Mythic | Modular architecture, multiple agent support | Free |
| Brute Ratel C4 | EDR-aware, built-in userland hook detection | Paid ($2.5k/yr) |

---

## Phase 13 — Exfiltration

> **TA0010 · Time: 2–5%**

---

### Exfiltration Methods

| Method | Details |
|--------|---------|
| **rclone + Cloud** | Sync staged data to attacker Mega.nz, SFTP, or S3 via rclone. AES-encrypted. Traffic looks like legitimate cloud sync. Throttle to stay under DLP volume thresholds. |
| **DNS Tunneling** | Dnscat2 or iodine. Very slow (20–40 kbps). Bypasses most egress filters that block HTTPS. Use for environments with strict egress whitelisting. Compress data before encoding. |
| **HTTPS Covert Channel** | Exfil through C2 HTTPS channel using Cobalt Strike download or equivalent. Throttled to match normal traffic volumes. Operates over same already-established channel. |
| **Email Exfiltration** | Send data out via SMTP using compromised Exchange with external relay. Attach compressed/encrypted archives. Looks like legitimate business communication if done with care. |

> ⚠️ **Warning:** Never encrypt real production data for ransomware simulation. Create a dedicated test directory, encrypt only those test files, document with screenshots. Destroying real data ends engagements and creates legal liability.

---

## Phase 14 — Impact Simulation

> **TA0040 · Time: 2–5% · Evidence: Critical**

Demonstrate business impact without causing it. The goal is timestamped, irrefutable proof-of-access to each defined crown jewel — translated into business risk language that resonates with executives.

---

### Crown Jewel Access Evidence Collection

| Objective | Evidence Collection Approach |
|-----------|------------------------------|
| **Domain Admin** | Screenshot of `whoami /all` showing DA membership, DA certificate via Certipy, DCSync output showing krbtgt hash. Timestamp visible. Hostname visible. DA group membership confirmation. |
| **Database Access** | Screenshot of SQL query against customer PII table (`SELECT TOP 10` — not full dump). Table name and row count visible. DB server hostname in query output. Timestamp in screenshot. |
| **Email System** | Screenshot of access to CEO/CFO inbox. Screenshot of sensitive email (redacted in report). O365 Global Admin access screenshot. Exchange admin console access. |
| **Financial System** | Read-only access proof to wire transfer system, ERP, financial reporting system. Screenshot without executing any transaction. Document the access path completely. |
| **Ransomware Sim** | Create isolated test directory. Encrypt test files with custom script. Screenshot encrypted directory with timestamp. Document specific systems impacted. **Never touch production data.** |

---

### Business Impact Translation

> **Example Translation**
>
> **Technical finding:** "We obtained Domain Admin credentials."
>
> **Business impact version:** "We obtained administrative control over all 2,847 Windows systems in the environment, including the servers hosting the customer database containing 4.2 million records. Under GDPR Article 83, exposure of this data carries maximum fines of €20 million or 4% of global annual turnover. We also obtained the encryption keys for the corporate backup system, meaning a threat actor could permanently destroy all backup data before deploying ransomware — eliminating the primary recovery option."
>
> **The second version gets budget approved. The first gets added to a backlog.**

---

### Cleanup Verification Checklist

- [ ] All persistence mechanisms removed — WMI subscriptions, scheduled tasks, registry keys, service entries, SSH keys, cloud backdoors
- [ ] All created accounts deleted — local accounts, domain accounts, cloud service principals, email forwarding rules
- [ ] All staged files deleted — tool uploads, implant binaries, collected data
- [ ] Operator notes reviewed for any residual sensitive data captured during engagement
- [ ] C2 infrastructure wiped — or documented as decommissioned for client records

---

## Phase 15 — Reporting & Debrief

> **POST-ENGAGEMENT · Time: 15–25% · Business Impact: Everything**

The report is the product. The engagement is just how you gather data for it. An operator who can compromise a Fortune 500 network but can't write a compelling report is worth half what they think. Write the report that changes things.

---

### Report Structure — The Definitive Template

| Section | Audience | What Goes In It |
|---------|----------|----------------|
| **Executive Summary** | CISO, Board, C-Suite | 1–2 pages max. Non-technical. What happened, what was reached, overall risk rating (CRITICAL/HIGH/MEDIUM), dwell time, objectives achieved vs. total. Zero jargon. |
| **Attack Narrative** | Security Leadership | Chronological story of the engagement. Write like a crime novel, not a log file. How you got in, what you found, how you moved, what you reached. Decisions you made. Why those decisions worked. |
| **Findings (Technical)** | Technical Teams | Each finding: descriptive title, CVSS score (justified), affected systems, evidence screenshot with timestamp, step-by-step reproduction, business impact statement, specific remediation steps with references. Not "patch this" — "apply patch KB5031358 on all DCs within 30 days." |
| **Attack Path Diagrams** | Technical + Leadership | Visual diagrams showing the actual compromise path from initial access to objective. Built in draw.io or Lucidchart. Shows how individual findings chain together into full compromise. |
| **ATT&CK Heatmap** | Blue Team / SOC | ATT&CK Navigator export with all techniques used highlighted. Shows Blue Team exactly where their detection coverage was absent. Enables direct mapping to detection rule development priorities. |
| **Remediation Roadmap** | IT / Engineering | Prioritized list: Critical (0–30 days), High (30–90 days), Medium (90–180 days), Low (roadmap). Include effort estimate, expected risk reduction, and specific references (CVE number, KB article, vendor advisory). |
| **Appendices** | Technical Teams | Complete command history with timestamps, full tool output, raw scan results, created account list, all captured data summary (hashed, not plaintext), methodology description. |

---

### Pre-Delivery Checklist

- [ ] All operator timestamps organized chronologically with action detail
- [ ] Every persistence mechanism documented **and verified removed**
- [ ] All created accounts removed and listed in appendix with creation/deletion dates
- [ ] Captured credentials listed with type (hash/plaintext), account, system — **never store plaintext in the report itself**
- [ ] Attack path diagram built and reviewed for accuracy with team
- [ ] ATT&CK Navigator heatmap exported and included
- [ ] Every finding has at least one timestamped screenshot as evidence
- [ ] Business impact statement written for non-technical reader — reviewed by someone non-technical
- [ ] All remediation steps reviewed for technical accuracy by the operator who found the issue
- [ ] Executive summary reviewed by non-technical person — they must understand it
- [ ] CVSS scores justified in writing — not just assigned arbitrarily
- [ ] All captured sensitive data (PII, credentials) destroyed per data handling policy and destruction documented with file hashes
- [ ] Debrief call scheduled within 5 business days of report delivery

---

### Reporting Toolkit

| Tool | Description | Cost |
|------|-------------|------|
| Ghostwriter | Full red team report platform — operation logs, client portal | Free |
| Pwndoc | Open-source pentest report generator, template library | Free |
| ATT&CK Navigator | TTP heatmap generation, export-ready, shareable | Free |
| PlexTrac | Enterprise report platform, finding management, trends | Paid |
| Vectr | Purple team tracking, coverage measurement | Free/Paid |
| Dradis | Collaboration platform, findings management | Free/Paid |

---

## A–Z Operator Encyclopedia

> The concepts, mindsets, techniques, and hard-won lessons that don't fit neatly into a phase checklist. Read these when you're stuck, when you're preparing, or when you want to think like a real operator rather than follow a recipe.

---

### A — Active Directory

**The Domain You Actually Own**

AD is present in virtually every enterprise and it is almost always misconfigured — not slightly, but embarrassingly. The average domain has:

- Kerberoastable service accounts with passwords unchanged since 2018
- ADCS misconfigs that allow any domain user to obtain a Domain Admin certificate (ESC1)
- Excessive ACL permissions granted years ago by admins who no longer work there
- At least one account with DCSync rights nobody remembers adding
- Unconstrained Delegation on systems that were "temporarily" set that way in 2019

**Your first hour with domain credentials always goes the same way:** run SharpHound, import to BloodHound CE, run the four standard attack path queries, run Certipy for ADCS issues, check what privileged groups your current user or their group memberships grant access to. You will find a path. In 12 years of engagements, every time.

**Tools:** BloodHound CE · SharpHound · Certipy · PowerView · Rubeus · Impacket

> *"In 12 years of red teaming, I have never failed to achieve Domain Admin in an Active Directory environment. Not once. The only variable is how long it takes — and that's almost always measured in hours, not days."*

---

### B — BloodHound

**God Mode for Active Directory**

BloodHound changed red teaming permanently. Before it, operators spent days manually mapping AD trust relationships. Now SharpHound collects in 3 minutes and the graph answers questions that would have taken a week to answer manually.

**Queries that consistently produce results:**
- "Shortest Path to Domain Admins"
- "Find AS-REP Roastable Users"
- "Find Kerberoastable Users with High Value Target"
- "Users with Foreign Domain Group Membership"
- "Shortest Paths to Unconstrained Delegation Systems"
- "Users with DCSync Rights"
- "Find Principals with DCSync Rights"

Run all of them, every time. The Community Edition (CE) is the actively maintained fork — use it, not the legacy version.

**Custom Cypher queries** let you find attack paths that built-in queries miss: accounts with write access to admin groups, GPO abuse paths, cross-domain trust exploitation routes. Learn basic Cypher — it multiplies your effectiveness tenfold.

**Tools:** BloodHound CE · SharpHound · AzureHound · PlumHound · GoodHound

> *"If you're not running BloodHound within 15 minutes of getting domain credentials, you are doing extra manual work for no reason. It finds paths in 30 seconds that would take a manual operator 2 days."*

---

### C — C2 Profile Engineering

**The Art of the Invisible Command Channel**

Default C2 framework signatures are burned. A default Cobalt Strike installation has hundreds of Sigma rules, Suricata rules, and YARA rules written specifically for it. The Beacon HTTP headers, the default URIs, the response structure, the TLS certificate fingerprint — all detectable in under a second by any mature NDR product.

**What makes a profile actually good:** It doesn't just mimic a real application's headers — it replicates the complete behavioral signature. Timing intervals, packet size distributions, response structure, error handling, HTTP status codes, response bodies. A profile mimicking Microsoft Graph API should return actual Graph-formatted JSON in responses.

If a Blue Team analyst captures the traffic and sends it to the SaaS vendor's security team saying "is this legit traffic?" — they should have to investigate to find out.

Invest 6–8 hours building a strong C2 profile before every engagement. That time investment protects your entire operation.

**Tools:** Cobalt Strike + Malleable C2 · Havoc · Sliver · C2concealer · SourcePoint

> *"A good C2 profile takes 6–8 hours to build. That 6 hours buys you weeks of undetected operation. The operator who skips this step gets their beacon killed on day two."*

---

### D — DCSync

**Domain Compromise Without Touching a DC**

DCSync impersonates a Domain Controller's replication process to pull every password hash from the real DC, entirely over the network, without requiring physical or RDP access to the DC itself. You need **DS-Replication-Get-Changes** and **DS-Replication-Get-Changes-All** rights.

**Why it's so powerful:** No disk access on the DC. No suspicious process on the DC. Looks like legitimate replication traffic. The only reliable detection is SIEM rules specifically watching for non-DC accounts making replication requests — which many organizations don't have configured correctly.

**The Golden Ticket endgame:**
1. Extract krbtgt hash via DCSync
2. Forge TGT valid for any account, any group membership, 10-year validity
3. Use for perpetual domain access that survives all password resets except krbtgt rotation

Remediating a Golden Ticket requires rotating krbtgt twice, 10 hours apart, while accepting authentication disruption. Most organizations do this incorrectly under incident response pressure.

**Tools:** secretsdump.py · Mimikatz (lsadump::dcsync) · Rubeus (golden) · ticketer.py

> *"DCSync is so clean it should be illegal. No disk access, no interactive logon, leaves almost no trace, gives you every password in the domain in 30 seconds."*

---

### E — EDR Bypass Mindset

**Research the Product, Not the Generic "EDR"**

Most junior operators treat EDR bypass as a puzzle to solve once with the right tool. Senior operators understand it as a continuous research problem that requires product-specific knowledge.

**The methodology that produces reliable results:**
1. Identify the exact EDR product and version number (Seatbelt, registry keys, running services)
2. Set up a lab VM with the identical product version
3. Understand how that specific EDR instruments the OS: which kernel callbacks it registers, which API calls it hooks in NTDLL, which ETW providers it subscribes to, which memory regions it scans
4. Find the gaps specific to that product and version
5. Test your full payload chain against the exact product before the engagement

CrowdStrike Falcon has fundamentally different blind spots than SentinelOne Singularity. Understanding the product is more important than any tool.

**Tools:** SysWhispers3 · ThreatCheck · ScareCrow · Freeze · PPLdump · Seatbelt

> *"The operator who understands how a specific EDR products instruments the OS wins against it. The one who tries random bypass tools loses eventually — and always at the worst moment."*

---

### F — Fileless Operations

**Living Entirely in Memory**

Fileless doesn't mean no files ever touch disk — it means your primary payload execution chain avoids writing to disk. Reflective DLL injection, process hollowing, PowerShell entirely in memory via AMSI-patched runspace, Beacon Object Files executing inside an existing process — all leave minimal or zero disk artifacts for forensics to find.

**The operational principle:** Your implant lives in the memory of a legitimate process. Your post-exploitation tools execute inside that implant's memory space (BOFs). Data is processed in memory and streamed out. Staging on disk is avoided. If you must write to disk, write to unmonitored paths (`%APPDATA%`, `%TEMP%`), use timestomping to match surrounding files, and delete immediately after use.

The shift to BOF-first development has been the single biggest improvement in red team tradecraft over the past 4 years. Develop or acquire BOF versions of all common post-exploitation tasks. The memory telemetry generated by in-process execution is dramatically less than spawning a new process.

**Tools:** BOF Framework · Donut · Inject-Assembly · PowerShell (AMSI-patched) · Masky

> *"The best forensic artifact is no artifact. The best SIEM event is no event. Build and operate with a fileless-first discipline and your detection surface shrinks by 80%."*

---

### G — Golden & Silver Tickets

**Forged Kerberos — The Skeleton Key**

A **Golden Ticket** is a forged Kerberos TGT signed with the domain's krbtgt account hash. Because Kerberos validates all tickets using that hash, controlling it means you can forge a ticket for any user, any group membership, any expiry — up to 10 years.

A **Silver Ticket** is forged using a specific service account's hash, granting access to that single service — useful when you need stealthy access to a specific resource without touching the KDC (Silver Tickets are validated locally, generating no KDC traffic).

**What makes Golden Tickets particularly powerful:** They survive all user password resets. The only remediation requires rotating krbtgt twice with a 10-hour gap — which causes authentication disruption domain-wide and which most organizations do not know how to do correctly.

> **Operational note:** Always document access. Never leave Golden Tickets active after engagement. The krbtgt hash captured must be handled under your data handling agreement.

**Tools:** Mimikatz (kerberos::golden) · Rubeus (golden/silver) · ticketer.py · secretsdump.py

> *"Obtaining the krbtgt hash is the end of the AD engagement. Every subsequent action is documentation and cleanup. The domain is yours until krbtgt is rotated twice correctly."*

---

### H — HTML Smuggling

**The Technique That Broke Email Gateways**

HTML Smuggling constructs the malicious payload client-side inside the user's browser using JavaScript. The email gateway inspects an HTML file — it sees no attachment, no embedded PE, no document with macros. Just JavaScript. The browser executes the JS, assembles the payload from a base64 blob via `Blob()` and `URL.createObjectURL()`, and triggers a browser-native download.

**Why it still works broadly in 2025:** Email security gateways cannot execute JavaScript in sandboxes at the scale needed for real-time analysis. Pair with an ISO container (which on unpatched systems avoids Mark-of-the-Web protections) or a signed EXE to complete the delivery chain.

**The current evolution:** SVG-based smuggling (SVG files can contain JavaScript), XHTML smuggling, and OneNote-embedded HTML objects. The underlying principle — assemble the payload client-side after delivery — remains constant across all variations.

**Tools:** Custom HTML templates · Demiguise · GadgetToJScript · ScareCrow (delivery)

> *"The day HTML smuggling stops working, something else will take its place. The delivery mechanism changes. The principle — assemble on the client side, bypass the gateway — is permanent."*

---

### I — Implant Architecture

**Engineer Your Agent Like Production Software**

Most operators take an off-the-shelf agent and use it as-is. Elite operators think about implant architecture the way a software engineer thinks about a production system: availability, failover, failure modes, recovery.

**Design questions for every implant deployment:**
- What happens when C2 goes down? (Automatic fallback to DNS channel)
- What happens if the beacon crashes? (Scheduled Task re-executes the loader)
- How does it reconnect after network interruption? (Exponential backoff with jitter)
- How does it fail safely if it detects analysis? (Exit cleanly, delete itself, no crash that generates a Windows Error Report)

**Staging considerations for long engagements:**
- Anti-sandbox checks before first callback
- Environment validation (specific domain? specific user logged in? business hours only?)
- Automatic self-deletion after X days of no C2 contact (dead man's switch)

**Tools:** Cobalt Strike (BOFs) · Havoc (Demon) · Sliver (multiplex) · Custom Rust/Nim/Go loaders

> *"Your implant is your presence in the environment. A flaky implant means a flaky operation. A dead implant in a segmented network means starting Phase 03 over. Build it like it has to last 90 days."*

---

### J — Jump Hosts & Pivoting

**Hopping Through Segmented Networks**

Network segmentation is the defense. Pivoting is the offense. Every firewall between you and your objective is a constraint to route around. Done well, your C2 traffic flows through compromised systems inside the perimeter — your external C2 IP never appears in the target network's internal traffic at all.

**Pivot techniques by stealth level:**

| Technique | Stealth | Notes |
|-----------|---------|-------|
| SOCKS proxy through beacon | Medium | CS socks5, Sliver socks5 — high flexibility |
| SSH tunneling | Low noise | Requires SSH access to compromised host |
| Ligolo-ng | Low noise | Layer-3 tunnel, zero dependencies, transparent routing |
| P2P beacons over SMB named pipes | Lowest | Chains through internal hosts, C2 never traverses external firewall |

Draw your network topology as you discover it. Mark each segment, the firewall between them, and which compromised hosts exist in each segment. Pivoting without a map creates confusion and missed opportunities.

**Tools:** Ligolo-ng · Chisel · CS SOCKS Proxy · SSH (-L/-R/-D) · frp · rpivot

> *"Segmentation only stops operators who don't know how to pivot. Learn to pivot and every firewall becomes a 20-minute problem, not a hard stop."*

---

### K — Kerberoasting

**The Gift That Never Stops Giving**

Kerberoasting has been a known attack since 2014. It still works in 2025 because organizations still run service accounts with passwords unchanged since their initial deployment, and those accounts often accumulate privilege over years of configuration drift.

Any domain user can request a Kerberos service ticket for any account with an SPN. That ticket is encrypted with the service account's RC4 or AES hash. Exfiltrate it, crack it offline. Service accounts with weak passwords crack in minutes on a GPU.

**The operational prioritization that matters:** Don't Kerberoast every SPN indiscriminately. Filter for service accounts that are members of privileged groups: Domain Admins (`adminCount=1`), Server Operators, Backup Operators, Account Operators. Run Rubeus with `/pwdsetdate:01/01/2023` to filter for accounts with recently changed passwords — those are harder targets.

**Wordlist strategy:** `rockyou.txt` top 50k + company-specific words (company name, city, year, product names) + common service account patterns (`ServiceName2019!`, `CompanyAD2021`).

**Tools:** Rubeus (kerberoast) · GetUserSPNs.py · Hashcat (-m 13100) · BloodHound (target prioritization)

> *"Every single domain has Kerberoastable accounts. The only question is whether the high-privilege ones have weak passwords. In most environments built before 2022, they do."*

---

### L — LSASS Without Getting Caught

**The Goldmine and the Minefield**

LSASS contains credentials for every user with an active session on the system. It is also the single most monitored Windows process. Every mature EDR has dedicated detection for LSASS access — ProcDump, Task Manager, comsvcs.dll MiniDump — all trigger immediate alerts.

**What actually works in mature environments in 2025:**

| Method | Technique | Notes |
|--------|-----------|-------|
| **Nanodump** | Custom minidump format | Doesn't match known LSASS dump file signatures |
| **HandleKatz** | Duplicate LSASS handle from another process | Many EDRs key on direct LSASS handle access — handle duplication from a different process bypasses this check |
| **PPLBlade** | Bypasses Protected Process Light | Abuse of arbitrary memory read primitive. Complex but necessary when LSASS PPL is enabled |
| **SSP Registration** | Register custom Security Support Provider | Receives credentials in plaintext as they're used. Passive collection, no LSASS access at all |

Process dumps are always offline. Never run Mimikatz on a live system in a monitored environment.

**Tools:** Nanodump · HandleKatz · PPLBlade · Mimikatz (offline only) · Masky

> *"Calling MiniDumpWriteDump on LSASS directly is how operators get caught in 2025. If your approach doesn't involve at least one layer of indirection, you're writing detection rules for the Blue Team."*

---

### M — MFA — It's Not a Hard Stop

**Every MFA Implementation Has Attack Surface**

Multi-factor authentication stops credential stuffing. It does not stop a determined, resourced operator. Every MFA implementation has identifiable attack surface:

| Attack | Description | Bypass Rate |
|--------|-------------|-------------|
| **Push notification fatigue** | Send 20–50 push notifications until the user approves. Call impersonating IT to guide them through "confirming." | ~60% |
| **AiTM proxy (Evilginx)** | Captures session token after successful MFA completion. Bypasses TOTP, Duo push, Microsoft Authenticator, hardware tokens, SMS — everything. | Near 100% |
| **Legacy auth endpoints** | NTLM authentication, basic auth, Exchange ActiveSync — these don't pass through MFA policies in many organizations. MFASweep finds every Microsoft endpoint that doesn't enforce MFA. | Varies |
| **SIM swapping** | Social engineer the mobile carrier for SMS-based MFA. | High complexity |
| **Voice phishing to helpdesk** | Impersonate an employee who "lost" their phone. Request MFA reset. | Varies by org culture |

**Tools:** Evilginx 3 · MFASweep · o365spray · AADInternals · Modlishka

> *"Every time a client says 'we have MFA everywhere, we're fine' — that's when you start looking for legacy authentication endpoints, AiTM delivery infrastructure, and helpdesk social engineering paths. MFA is necessary. It is not sufficient."*

---

### N — NTLM Relay

**The Protocol Vulnerability That Never Ages**

NTLM relay attacks have existed for over two decades. They still work broadly in 2025 because NTLM is still used throughout Windows networks, and because enforcing SMB signing network-wide breaks things that IT teams avoid dealing with.

**The modern chain that compromises fully patched environments with no CVE exploitation:**

```
1. mitm6 poisons IPv6 DHCP → becomes the DNS server
2. Responder captures NTLM challenges from network broadcast protocols
3. ntlmrelayx.py relays authentication to LDAP (without signing) on a DC
4. Create a computer account in the domain (MachineAccountQuota allows by default)
5. Perform Resource-Based Constrained Delegation (RBCD) attack
6. Impersonate Domain Admin to target system → DA
```

This entire chain requires **no CVE, no patch failure, no configuration error** beyond the default Windows installation. SMB signing enforcement and reducing MachineAccountQuota to 0 defeats it — but most organizations haven't done either.

**Tools:** Responder · ntlmrelayx.py · PetitPotam · SpoolSample · mitm6

> *"NTLM relay is the attack that never ages. As long as SMB signing isn't universally enforced (and it isn't, in most enterprises), this chain works from a single compromised host inside the network."*

---

### O — OPSEC

**The Discipline That Keeps You Employed**

Operational Security is not a checklist. It is a mindset that governs every decision throughout an engagement.

**OPSEC failures from real engagements that burned operators:**
- Using a personal email to register a campaign domain (registrar WHOIS subpoena)
- VPN gap exposure: personal IP appeared in target firewall logs during VPN reconnect window
- Reusing the same compiled binary across two different clients (same PE hash in both forensic reports)
- Running OSINT from the same IP used for C2 (correlated in client NDR logs)
- Forgetting to delete operator notes from a staged file on the target system — notes included client name, engagement dates, and team Slack channel
- Posting a "generic" technical blog post about a technique used in an active engagement, timing-correlated by client's threat intel team

**Core OPSEC principles:**
- Dedicated VMs per campaign (fresh Kali/ParrotOS)
- No personal accounts anywhere in the attack chain
- VPN to redirector before all C2 access
- Signal or Matrix for team comms only
- Encrypted note-taking (Cryptpad)
- Separate hardware where possible

**Tools:** Dedicated VMs per campaign · Mullvad VPN · Signal · Protonmail · Cryptpad

> *"OPSEC failures don't just burn the engagement. They burn your reputation, your company's reputation, and in certain jurisdictions involving certain actions, potentially your freedom."*

---

### P — Phishing in 2025

**Still Number One. Always Will Be.**

Phishing remains the dominant initial access vector in every threat intelligence report published in 2024–2025. This isn't going to change. Phishing targets the hardest problem in security: human judgment under time pressure and task load.

**What's working right now:**

| Technique | Description | Effectiveness |
|-----------|-------------|--------------|
| **Business Email Compromise** | High-context lures referencing real internal projects (from LinkedIn), real vendor names, real employee names. | 40–60% click rates vs 2–5% generic |
| **QR Code Phishing (Quishing)** | QR codes evade URL scanning on email gateways. Mobile cameras open URLs in mobile browsers with weaker security posture. | Increasingly effective |
| **Callback Phishing** | Fake invoice or security alert with only a phone number. Target calls in. Social engineer over the phone into downloading remote access tool. No malicious URL, no attachment. | Bypasses all technical controls |
| **Teams/Slack Phishing** | Send phishing messages via compromised accounts or external federation. Users trust their internal collaboration tools more than email. | High trust factor |

The highest-performing campaigns share one trait: the lure looks exactly like something the target was already expecting to receive.

**Tools:** Evilginx 3 · GoPhish · EvilnoVNC · Modlishka · TeamsPhisher

> *"The best phish is one the target was already waiting to receive. Do your OSINT. Find the right context. Write the lure that makes the click feel inevitable."*

---

### Q — Quick Wins — First 30 Minutes

**What to Check Before Anything Else**

When you get your first foothold, run these checks before anything complex. Most engagements produce at least one immediate escalation path from this list alone.

| Check | Command/Method | What It Finds |
|-------|----------------|---------------|
| **1. User group membership** | BloodHound "owned" marking | Unexpected privileged group membership |
| **2. GPP passwords in SYSVOL** | `findstr /s /i cpassword \\DOMAIN\SYSVOL` | Decryptable passwords in XML files |
| **3. AlwaysInstallElevated** | `reg query HKLM\...\Policies\Installer` | HKLM and HKCU both set → MSI installs as SYSTEM |
| **4. Writable service binary paths** | `PowerUp.ps1 Invoke-AllChecks` | Service binary replacement → SYSTEM |
| **5. SMB signing disabled** | `nxc smb [subnet] --gen-relay-list` | Network-wide NTLM relay is live |
| **6. ADCS ESC1** | `certipy find --vulnerable` | Any user can get DA certificate in 30 seconds |
| **7. SeImpersonatePrivilege** | `whoami /priv` | GodPotato → SYSTEM immediately |
| **8. AS-REP Roastable users** | `Rubeus asreproast` | Get hashes with no auth required |

**Tools:** WinPEAS · PowerUp · Snaffler · Certipy · Rubeus · GodPotato

> *"The most embarrassing path to Domain Admin is the one sitting there in the first 10 minutes that you missed because you rushed to something more complex. Slow down. Run the checklist first."*

---

### R — Redirectors

**The Infrastructure That Keeps Operations Alive**

A redirector is a VPS sitting between the target environment and your C2 server. When Blue Team investigators discover and analyze your C2 traffic (and they will if the engagement lasts long enough), they find the redirector IP — not your real C2.

**Correct setup:**
- Nginx with profile-aware routing: correct URI + correct User-Agent → `proxy_pass` to real C2
- Everything else → `301` redirect to legitimate website
- The C2 server firewall only accepts connections from the redirector IPs
- The C2 server has no domain name and no open ports except what the redirector connects on

**When the redirector gets burned:** Blue Team blocks the IP. Spin up a new VPS ($6/month, 10 minutes). Your C2 server is untouched. Update the DNS record for your C2 domain to the new redirector. The operation continues.

Always build **2 redirectors per engagement**: HTTPS and DNS. When one gets blocked, the fallback activates automatically.

**Tools:** Nginx (profile-aware routing) · Apache (mod_rewrite) · socat · Cloudflare Workers · DigitalOcean/Vultr VPS

> *"Your C2 IP getting burned ends the operation. Your redirector IP getting burned costs $6 and 10 minutes. There is no excuse for exposing your real C2 server to the internet directly."*

---

### S — Syscalls

**Bypassing EDR at the Lowest Instrumentation Layer**

EDR products instrument Windows primarily by hooking NTDLL.dll functions in every process's memory. When your code calls `NtAllocateVirtualMemory`, the EDR's hook intercepts, inspects arguments, decides if the call is suspicious, then passes it to the kernel. This is where most EDR detection happens.

**Direct syscalls bypass this entirely.** Instead of calling through NTDLL (where hooks live), you directly invoke the Windows kernel using the raw syscall instruction with the correct System Service Number (SSN). SysWhispers3 generates the assembly stubs automatically.

**The evolution of syscall evasion:**

| Generation | Technique | What It Defeats |
|------------|-----------|----------------|
| Generation 1 | Direct syscalls | NTDLL API hook inspection |
| Generation 2 | Indirect syscalls | Syscall instruction origin detection |
| Generation 3 | Unhooking NTDLL | Restores original NTDLL from disk |
| Generation 4 | Kernel-mode evasion | Bypasses PatchGuard-aware kernel EDR |

**Tools:** SysWhispers3 · HellsGate · HalosGate · RecycledGate · FreshyCalls

> *"If you don't understand how NTDLL hooking works mechanically — not just conceptually — you cannot reliably bypass it. Read the Windows Internals book. Then read it again."*

---

### T — Threat Intelligence Integration

**Know Who Your Client Actually Faces**

Red teaming without threat intelligence is just pentesting with a fancier name. The entire value proposition of adversary emulation is replicating the TTPs of the actual threat actors targeting your client.

**The pre-engagement threat intelligence workflow:**

1. Identify client's industry sector (healthcare, finance, defense, energy, etc.)
2. Pull MITRE ATT&CK Groups filtered by sector
3. Identify the 2–3 most active/relevant threat actor groups
4. Find their most recent campaign reports (Mandiant, CrowdStrike, SentinelOne, CISA advisories, industry ISACs)
5. Map their documented TTPs to ATT&CK technique IDs
6. Build an engagement TTP list that replicates their initial access methods, execution techniques, persistence mechanisms, C2 protocols, and target data types
7. Design your phishing lures and payloads to resemble their documented campaigns

**Industry → Threat Actor Mapping (Current 2025):**

| Industry | Primary Threat Actors |
|----------|----------------------|
| Financial Services | FIN7, Scattered Spider, Lazarus Group, FIN12 |
| Defense / Government | APT10, APT41, APT29, Volt Typhoon |
| Healthcare | FIN12, Lazarus Group, Vice Society |
| Energy / Critical Infrastructure | Sandworm, Volt Typhoon, ALPHV |
| Technology / SaaS | Scattered Spider, APT10, UNC3944 |

**Tools:** MITRE ATT&CK · OpenCTI · MISP · Mandiant Threat Reports · CISA Advisories · FS-ISAC

> *"The question your report should answer isn't 'can we be hacked?' It's 'would we survive APT29 targeting us this quarter?' Answer that specific question and your report becomes impossible to deprioritize."*

---

### U — Unconstrained Delegation

**The Most Dangerous AD Misconfiguration**

Unconstrained Delegation means: any user who authenticates to this computer will have their TGT (Ticket Granting Ticket) cached in memory on that computer — including Domain Controllers when they authenticate for legitimate services.

**The exploitation chain that compromises fully patched environments with no CVE:**

```
1. BloodHound query "Find Systems with Unconstrained Delegation" → identify target systems
2. Gain admin on the unconstrained delegation system (any PrivEsc technique)
3. Start Rubeus in monitor mode: rubeus monitor /interval:5 /targetuser:DC01$
4. Coerce a Domain Controller to authenticate to your controlled system:
   - SpoolSample.exe DC01 DELEGATION-HOST (PrinterBug)
   - PetitPotam.py DELEGATION-HOST DC01 (MS-EFSRPC)
5. DC machine account TGT arrives in memory — captured by Rubeus
6. Pass-the-Ticket → authenticate as DC machine account
7. DCSync the domain — extract all hashes including krbtgt
8. Full domain compromise
```

This works on fully patched Server 2022 with **no vulnerabilities**. It's a configuration issue, not a software bug.

**Tools:** BloodHound (find targets) · Rubeus (monitor+dump) · SpoolSample (PrinterBug) · PetitPotam (MS-EFSRPC)

> *"Unconstrained delegation on any non-DC computer is a direct path to full domain compromise. Find it on day one of every engagement. It's present in more than half of the environments I've assessed."*

---

### V — Vulnerability Chaining

**Three Mediums Equal One Critical**

Standalone vulnerabilities often look manageable in isolation. What traditional pentests present as a list of individual findings, red team engagements reveal as interconnected attack chains where the combined impact dwarfs any individual issue's severity.

**A real chain from a 2024 engagement:**

```
Finding 1: Exposed internal Jenkins server — no auth required (CVSS: Medium)
       ↓
Finding 2: Jenkins build config contained plaintext service account credentials (CVSS: Medium)
       ↓
Finding 3: Service account had GenericWrite on a high-privilege AD security group (CVSS: Medium)
       ↓
Finding 4: Added operator-controlled user to "Server Admins" group (CVSS: High)
       ↓
Finding 5: Server Admin group had local admin on all servers including ADCS host (CVSS: Critical)
       ↓
Finding 6: ADCS ESC1 misconfiguration → Domain Admin certificate in 30 seconds (CVSS: Critical)
       ↓
Result: Full domain compromise from the internet via 6-hop chain.
        Each finding: Medium. Combined: Critical.
```

Your report must visualize this chain — draw it explicitly, show the connection between finding #1 and finding #6. That diagram is the single most persuasive element in any red team report.

**Tools:** BloodHound (chain visualization) · Draw.io / Lucidchart · ATT&CK Navigator · Ghostwriter

> *"The most important sentence in your report connects finding #2 to finding #7 and shows how three Medium-severity issues produced full domain compromise. Write that sentence clearly and the remediation gets funded."*

---

### W — WMI — Windows Management Instrumentation

**The Stealthiest Execution Primitive in Windows**

WMI is a legitimate Windows management framework present in every Windows installation since NT 4.0. IT departments use it daily. Security teams can't just block it. And it provides nearly everything an attacker needs: remote code execution, process enumeration, system information, event subscriptions for persistence, and lateral movement.

**Why wmiexec beats PsExec for lateral movement in monitored environments:**

| Attribute | PsExec | wmiexec |
|-----------|--------|---------|
| Disk artifact | Binary written to ADMIN$ | None (fileless mode) |
| Service installation | Yes (EventID 7045) | No |
| Protocol | SMB | WMI/DCOM (port 135) |
| Detection profile | High | Low-Medium |
| Process tree | Suspicious parent-child | Legitimate WMI host |

WMI Permanent Event Subscriptions for persistence are among the stealthiest available: no new service, no new registry RunKey, only detectable by querying WMI itself for subscriptions.

**Tools:** wmiexec.py (Impacket) · Invoke-WMIMethod · SharpWMI · WMIImplant

> *"WMI is in every Windows environment, used by every IT team, cannot be removed, and is incredibly difficult to monitor without breaking legitimate operations. The perfect attacker tool hiding in plain sight since 1998."*

---

### X — XXE & SSRF

**Web App Vulnerabilities That Reach the Internal Network**

XXE (XML External Entity injection) and SSRF (Server-Side Request Forgery) are particularly valuable because they're web application vulnerabilities that open windows into the internal network and cloud metadata infrastructure — bypassing all network perimeter controls by making the target server send requests on your behalf.

**The SSRF-to-cloud-compromise chain (seen repeatedly in real engagements):**

```
1. Find SSRF in a public-facing web application
   (common in: XML parsers, webhook URLs, URL preview functionality,
    PDF generators, webhook testing features)
2. Point SSRF at AWS EC2 metadata endpoint:
   http://169.254.169.254/latest/meta-data/iam/security-credentials/
3. Retrieve temporary IAM credentials (AccessKeyId, SecretAccessKey, SessionToken)
4. Use credentials with aws-cli to enumerate the entire AWS account
5. Escalate privileges via IAM privilege escalation:
   - Attach AdministratorAccess policy to a new user
   - Abuse Lambda/CloudFormation to elevate
6. Full AWS account compromise:
   - All S3 buckets
   - All EC2 instances
   - All RDS databases
   - All secrets in Secrets Manager
```

This chain involves **zero malware, zero endpoint compromise**, and touches nothing that endpoint detection tools monitor.

**Tools:** Burp Suite Pro · SSRFmap · Gopherus · aws-cli · Pacu (AWS post-exploit)

> *"SSRF to cloud metadata is one of the cleanest attack chains available — web app to full cloud admin with no malware, no endpoint artifacts, no EDR alerts. Look for it on every external assessment that touches cloud infrastructure."*

---

### Y — Your Report Changes Things

**Or It Sits in a Shared Drive Until the Next Assessment**

Most penetration test reports land in a SharePoint folder, get a brief review, generate a handful of Jira tickets, and are forgotten within six months. The organization is marginally more secure than before the engagement. The red team spent three weeks doing exceptional technical work that produced no lasting change.

This happens because operators write reports for other operators, not for the people who control security budgets and implementation priorities.

**The report that changes things:**
- Opens with a business impact statement that translates technical findings to regulatory exposure, revenue risk, and operational disruption potential
- Gives the CISO something they can take to the board
- Shows the attack path visually — a diagram that makes the 5-hop compromise chain instantly comprehensible to non-technical leadership
- Provides an opinionated remediation roadmap with timelines, effort estimates, and expected risk reduction
- Ends with a debrief session where the red team presents to both technical and leadership audiences simultaneously

**The report that gets budget approved, ADCS misconfigurations fixed this quarter (not next year), and creates lasting security improvement.**

**Tools:** Ghostwriter · Pwndoc · Dradis · PlexTrac · Vectr

> *"A great hack with a mediocre report produces no lasting security improvement. A mediocre engagement with a great report moves the security needle for 3 years. Learn to write. It multiplies everything else you do."*

---

### Z — Zero Day Mindset

**You Don't Need Zero Days. You Need to Think Like a Researcher.**

Junior operators think elite hackers use zero days. The reality: elite red team engagements are almost universally won with techniques that are years or decades old, exploiting misconfigurations that have existed since the domain was built, against human beings who are susceptible to social engineering regardless of how much training they receive.

**The zero day mindset isn't about finding new CVEs.** It's about looking at every system with the curiosity of a researcher and asking:
- What does this system **trust**?
- What **assumptions** did the person who configured this make?
- Where does that assumption **break**?
- What happens at the **edge** of designed behavior?

Apply these questions to everything:
- **Active Directory** — What trust relationships exist between objects?
- **Cloud configurations** — What does this service account have implicit access to?
- **Web applications** — What does this server trust from clients without validation?
- **Network architecture** — What implicit trust exists between segments?
- **Human processes** — What does the helpdesk assume about callers?

The operators who look like they have magic are usually just asking better questions about systems that everyone else takes for granted. The zero day mindset is available to anyone willing to think carefully. No bug bounty required.

**Self-study resources:**
- IRED.team — ired.team
- HackTricks — book.hacktricks.xyz
- RedTeam.guide — redteam.guide
- MITRE ATT&CK — attack.mitre.org
- Exploit-DB — exploit-db.com
- NVD / CISA KEV — nvd.nist.gov / cisa.gov/known-exploited-vulnerabilities-catalog

**Tools:** IRED.team · HackTricks · RedTeam.guide · MITRE ATT&CK · Exploit-DB · NVD / CISA KEV

> *"The operators who look like they have magic are asking better questions about the systems everyone else ignores. No zero day required — just genuine curiosity about how things actually work vs. how they're supposed to work."*

---

## Reference Resources

### Standards & Frameworks

| Standard | URL | Description |
|----------|-----|-------------|
| MITRE ATT&CK | attack.mitre.org | Primary technique reference, Navigator heatmaps |
| MITRE ATT&CK Navigator | attack.mitre.org/resources/navigator | TTP heatmap generation |
| PTES | pentest-standard.org | Penetration Testing Execution Standard |
| OSSTMM | isecom.org | Open Source Security Testing Methodology |
| NIST SP 800-115 | csrc.nist.gov | Technical Guide to Information Security Testing |
| CBEST | bankofengland.co.uk | Bank of England intelligence-led testing framework |
| TIBER-EU | ecb.europa.eu | European Central Bank threat intelligence-based testing |

### Essential Reference Sites

| Site | URL | Description |
|------|-----|-------------|
| IRED.team | ired.team | Red team notes, techniques, and research |
| HackTricks | book.hacktricks.xyz | Comprehensive hacking techniques reference |
| LOLBAS | lolbas-project.github.io | Living off the land binaries and scripts |
| GTFOBins | gtfobins.github.io | Unix binaries for privilege escalation |
| C2 Matrix | thec2matrix.com | C2 framework comparison |
| RedTeam.guide | redteam.guide | Red teaming methodology guide |
| CISA KEV | cisa.gov/known-exploited-vulnerabilities-catalog | Known exploited vulnerabilities catalog |

### Master Toolbox — Complete Reference

| Tool | Category | Cost | Notes |
|------|----------|------|-------|
| **BloodHound CE** | AD Recon | Free | Run within 15 minutes of domain access |
| **SharpHound** | AD Collection | Free | Data collector for BloodHound |
| **Certipy** | ADCS | Free | ESC1-13 enumeration and exploitation |
| **Rubeus** | Kerberos | Free | Kerberoast, AS-REP, ticket operations |
| **Impacket Suite** | Protocol attacks | Free | secretsdump, wmiexec, psexec, ntlmrelayx |
| **Mimikatz** | Credential access | Free | Use offline on captured dump files |
| **Nanodump** | LSASS dump | Free | Custom minidump format, bypasses detection |
| **PowerView** | AD enum | Free | ACL queries, AD object enumeration |
| **Cobalt Strike** | C2 | Paid ($3.5k/yr) | Industry standard, malleable profiles |
| **Havoc C2** | C2 | Free | Best free alternative |
| **Sliver** | C2 | Free | Go-based, multiple transports |
| **Mythic** | C2 | Free | Modular, multiple agent support |
| **Brute Ratel C4** | C2 | Paid ($2.5k/yr) | EDR-aware commercial C2 |
| **Evilginx 3** | Phishing/AiTM | Free | Session token capture, bypasses MFA |
| **GoPhish** | Phishing | Free | Campaign management platform |
| **SysWhispers3** | EDR Bypass | Free | Direct syscall stub generator |
| **ThreatCheck** | AV testing | Free | Identify bytes triggering Defender/AMSI |
| **ScareCrow** | Payload | Free | Shellcode loader with EDR bypass |
| **Freeze** | Payload | Free | ETW/AMSI patching, PE cloaking |
| **Donut** | Payload | Free | PE/.NET to shellcode converter |
| **WinPEAS** | PrivEsc | Free | Windows privilege escalation checker |
| **LinPEAS** | PrivEsc | Free | Linux privilege escalation checker |
| **GodPotato** | PrivEsc | Free | SeImpersonate → SYSTEM, Win10-11 |
| **UACME** | UAC Bypass | Free | 65+ bypass techniques |
| **Snaffler** | Discovery | Free | Sensitive file finder across shares |
| **Seatbelt** | Discovery | Free | Host security posture survey |
| **NetExec (nxc)** | Lateral movement | Free | Mass validation, module library |
| **Evil-WinRM** | Lateral movement | Free | WinRM shell with bypass options |
| **Ligolo-ng** | Pivoting | Free | Layer-3 tunneling through implants |
| **Chisel** | Pivoting | Free | HTTP-based TCP tunneling |
| **Responder** | NTLM capture | Free | NBT-NS/LLMNR poisoning |
| **ntlmrelayx.py** | NTLM relay | Free | Relay authentication attacks |
| **mitm6** | IPv6 attack | Free | IPv6 DNS poisoning |
| **Hashcat** | Password crack | Free | GPU-accelerated, all hash types |
| **Burp Suite Pro** | Web app | Paid ($499/yr) | Indispensable for web testing |
| **nuclei** | Scanning | Free | 5000+ CVE and misconfig templates |
| **Shodan** | OSINT | Free/Paid | Internet device scanner |
| **Dehashed** | OSINT | Paid ($15/mo) | Leaked credential database |
| **subfinder** | OSINT | Free | Subdomain enumeration |
| **httpx** | OSINT | Free | Live host probing and fingerprinting |
| **truffleHog** | OSINT | Free | Git secret scanning |
| **Ghostwriter** | Reporting | Free | Full red team report platform |
| **Pwndoc** | Reporting | Free | Open-source report generator |
| **ATT&CK Navigator** | Reporting | Free | TTP heatmap generation |
| **PlexTrac** | Reporting | Paid | Enterprise report platform |
| **AzureHound** | Cloud | Free | BloodHound collector for Entra ID |
| **CloudFox** | Cloud | Free | Multi-cloud attack surface enum |
| **Pacu** | Cloud | Free | AWS post-exploitation framework |
| **AADInternals** | Cloud | Free | Azure AD / M365 attack toolset |
| **Whisker** | Shadow Creds | Free | msDS-KeyCredentialLink attack |
| **PetitPotam** | Auth coercion | Free | MS-EFSRPC authentication coercion |
| **Flipper Zero** | Physical | Paid (~$200) | RFID/NFC/RF for physical ops |
| **WiFi Pineapple** | Physical | Paid (~$120) | Rogue AP for Wi-Fi attacks |

---

## Document Information

| Field | Value |
|-------|-------|
| **Document** | Red Team Field Manual (RTFM) |
| **Edition** | 2025 v6.0 Final |
| **Framework** | MITRE ATT&CK v15 |
| **Phases** | 16 Total |
| **Techniques** | 200+ |
| **A–Z Coverage** | 26 Entries (A through Z) |
| **Last Updated** | 2025 |
| **Classification** | Authorized Personnel Only |

---

> **Legal Notice:** This document is for authorized security testing only. All techniques described require explicit written authorization from the system owner before use. Unauthorized access to computer systems is a criminal offense under the Computer Fraud and Abuse Act (CFAA), Computer Misuse Act, and equivalent statutes in virtually every jurisdiction globally. The authors assume no liability for unauthorized use of any techniques, tools, or methodologies described herein.
