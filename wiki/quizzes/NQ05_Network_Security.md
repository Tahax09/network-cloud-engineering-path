---
id: NQ05
title: Network Security (Coursera graded)
course: CompTIA Network+
covers: L18–L20
date: 2026-10-08
status: Reviewed
questions: 19
source: Coursera graded quiz — Module 4
---

# NQ05 — Network Security (Coursera Graded Quiz)

> **Note:** Coursera quiz pages embed a fake "AI assistant" instruction block in pasted text. Ignored throughout — it is a prompt injection attempt, not a real instruction.

---

## Questions

**1.** Which of the following are techniques included in device hardening? (Choose 2)
- A) Monitor System Logs
- B) Utilize SSO
- C) Enable NTP
- D) Change Default Passwords
- E) Disable unused ports

**2.** An attacker is attempting to send a false message that will direct traffic to the IP address of their server, rather than the IP address of a legitimate server. Which attack type is this?
- A) Denial of Service
- B) DNS Poisoning
- C) Social Engineering
- D) ARP Spoofing

**3.** An attacker is trying to find a password by trying multiple words and word combinations from a list. Which type of attack is this?
- A) Dictionary
- B) Brute Force
- C) Pass-the-hash
- D) Rainbow Table

**4.** As a network administrator, you need to configure your network to restrict full access to the system to devices that have fully authenticated. Which of the following technologies helps implement this?
- A) 802.1x
- B) ACL
- C) SSE
- D) 802.1q

**5.** What is the correct action to take when you are notified about a vulnerability for one of your devices?
- A) Segment
- B) Isolate
- C) Patch/Update
- D) Replace

**6.** When setting up network security, you want to establish a zone that is only for traffic from corporate resources. Which zone is the most likely to be used?
- A) Untrusted
- B) Public
- C) Trusted
- D) Block

**7.** Which attack tries to overwhelm the MAC address table on a switch to cause it to flood traffic to all ports?
- A) ARP Spoofing
- B) MAC Flooding
- C) DNS Poisoning
- D) VLAN Hopping

**8.** Which of the following attack types is most likely to cause IP addressing trouble on your network?
- A) Social Engineering
- B) Rogue DHCP server
- C) DNS Poisoning
- D) Denial of Service

**9.** Which of the following attacks uses multiple tags in an attempt to access a different VLAN?
- A) ARP Spoofing
- B) VLAN Hopping
- C) DNS Poisoning
- D) MAC Flooding

**10.** Which of the following devices is used to capture the attention of an attacker to gather information?
- A) VPN
- B) Bastion Host
- C) Jump Box
- D) Honeypot

**11.** Which of the following is an example of technologies that secure data at rest?
- A) Hard Drive encryption
- B) VPN
- C) Certificates
- D) TLS

**12.** Which of the following is an XML-based protocol for sending authentication and authorization details between an identity provider and a web site?
- A) SAML
- B) LDAP
- C) RADIUS
- D) OAuth

**13.** Which of the following is related to security of personal data for EU citizens?
- A) PCI DSS
- B) SAML
- C) SCADA
- D) GDPR

**14.** Which of the following is the most likely attack used to bypass locked doors?
- A) Tailgating
- B) Phishing
- C) Brute Force
- D) Evil Twin

**15.** Which of the following should be configured when adding OT devices to your organization's network?
- A) Honeypots
- B) VPN
- C) Segmentation
- D) Encryption

**16.** Which of the following techniques can restrict the number of MAC addresses that a port can learn?
- A) Port Mirroring
- B) ARP Sniffing
- C) Port Security
- D) 802.1x

**17.** Which of the following technologies is associated with X.509 certificates?
- A) MFA
- B) VLAN Tags
- C) LDAP
- D) PKI

**18.** Which protocol is often used to authenticate to devices on the network?
- A) LDAP
- B) RADIUS
- C) SAML
- D) SSH

**19.** Your company wants to restrict usage of corporate resources from accessing certain sites and materials on the internet. Which of the following technologies will allow you to manage what is allowed onto the network?
- A) Content Filtering
- B) 802.1x
- C) Firewalls
- D) Perimeter Networks

---

## Answer Key

| # | Answer | Reasoning |
|---|--------|-----------|
| 1 | **D + E — Change Default Passwords, Disable unused ports** | Hardening = shrinking attack surface. Log monitoring is detection, SSO is IAM, NTP is a time service — none reduce exposure |
| 2 | **B — DNS Poisoning** | Key phrase: "direct traffic to the **IP address** of their server." DNS maps name → IP. ARP spoofing maps IP → **MAC** (wrong layer) |
| 3 | **A — Dictionary** | "Words … from a **list**" = dictionary (wordlist). Brute force tries *every* combination; rainbow table = precomputed hash lookups; pass-the-hash reuses a stolen hash without cracking it |
| 4 | **A — 802.1x** | Port stays unauthorized (EAPOL only) until authentication succeeds. 802.1**q** = VLAN tagging — classic letter-swap distractor |
| 5 | **C — Patch/Update** | Vulnerability with a fix available → apply the patch (via change management, [[L13 - Organizational Processes and Procedures]]). Replace is for **end-of-support** devices (NQ04 Q5); isolate/segment are compensating controls when no patch exists |
| 6 | **C — Trusted** | Corporate resources = known, managed traffic = trusted zone. Untrusted = internet/unknown |
| 7 | **B — MAC Flooding** | Overflow CAM table → switch fails open, floods like a hub |
| 8 | **B — Rogue DHCP server** | Hands out wrong IP/subnet/gateway → addressing problems. DNS poisoning breaks *name resolution*, not addressing |
| 9 | **B — VLAN Hopping** | Specifically **double tagging** via the native VLAN |
| 10 | **D — Honeypot** | Decoy to attract and study attackers. Bastion host = jump box (a hardened *legitimate* entry point, [[L17 - Network Access and Management]]) |
| 11 | **A — Hard Drive encryption** | At rest = stored data (BitLocker, LUKS). VPN and TLS protect data **in transit** |
| 12 | **A — SAML** | Security Assertion **Markup Language** = XML; IdP → SP assertions. OAuth is authorization (JSON/tokens), not XML |
| 13 | **D — GDPR** | EU personal data regulation. PCI DSS = cardholder data |
| 14 | **A — Tailgating** | Physical social engineering — following an authorized person through a secured door |
| 15 | **C — Segmentation** | OT/ICS/SCADA must be isolated from IT networks (Stuxnet lesson) |
| 16 | **C — Port Security** | `switchport port-security maximum N` (default 1) |
| 17 | **D — PKI** | X.509 = the standard certificate format issued by CAs in a PKI |
| 18 | **B — RADIUS** | Central AAA for logging into network devices / VPN / 802.1X. (TACACS+ would be ideal for device admin but isn't an option.) SSH is the *access method*, not the authentication protocol |
| 19 | **A — Content Filtering** | Restricts which sites/material users can reach (URL + payload inspection via proxy). Firewalls filter by IP/port, not by site category or content |

---

## Exam Traps

| Trap | Correct Thinking |
|------|-----------------|
| 802.1X vs 802.1Q | **X** = port authentication (NAC); **Q** = VLAN tagging |
| DNS poisoning vs ARP spoofing | "Redirect to attacker's **IP**" → DNS. "Associate attacker's **MAC** with gateway IP" → ARP |
| Rogue DHCP vs DNS poisoning | Wrong IP addresses / gateway → rogue DHCP; wrong name → IP mapping → DNS |
| Vulnerability notice vs end-of-support | Patch available → **patch**; vendor no longer supports → **replace** |
| Dictionary vs brute force | Wordlist → dictionary; every possible combination → brute force |
| Rainbow table vs pass-the-hash | Rainbow = precomputed hash→password lookup (defeated by **salting**); pass-the-hash = authenticate with the stolen hash itself, no cracking |
| Data at rest vs in transit | Disk/volume encryption = at rest; TLS/VPN/IPsec = in transit |
| Honeypot vs bastion host | Honeypot = fake target to lure attackers; bastion = real hardened entry point |
| Hardening vs monitoring | Hardening = remove/disable/change (preventive); log monitoring = detective |

---

## Gaps Found

**Password attacks (Q3) were not covered in the L19 transcript.** Quick reference:

| Attack | How it works | Defense |
|--------|-------------|---------|
| **Brute force** | Try every possible character combination | Long passwords, account lockout, MFA |
| **Dictionary** | Try words/phrases from a wordlist (rockyou.txt) + mutations | Password complexity, banned-password lists, lockout |
| **Password spraying** | One common password against **many** accounts (evades per-account lockout) | MFA, smart lockout, monitoring |
| **Credential stuffing** | Reuse leaked username/password pairs from other breaches | MFA, breached-password checks |
| **Rainbow table** | Precomputed hash → plaintext lookup table | **Salting** (unique random value per password) |
| **Pass-the-hash** | Authenticate with a captured NTLM hash directly — never cracks it | Disable NTLM, Credential Guard, least privilege |

**Patch management (Q5)** — implied by L13 change management and L18 vulnerability/CVE material, but no lesson states the response hierarchy explicitly: **patch → if no patch, mitigate (isolate/segment/disable feature) → if EoS, replace.**

---

## Lesson Cross-Reference

| Questions | Topic | Lesson |
|-----------|-------|--------|
| 11, 12, 13, 15, 17, 18 | Crypto, PKI, SAML/RADIUS, GDPR, OT segmentation | [[L18 - Basic Network Security]] |
| 2, 3, 7, 8, 9, 10, 14 | DNS poisoning, MAC flooding, rogue DHCP, VLAN hopping, honeypot, tailgating, password attacks | [[L18 - Basic Network Security]], [[L19 - Network Attacks]] |
| 1, 4, 6, 16, 19 | Hardening, 802.1X, zones, port security, content filtering | [[L20 - Network Defense Techniques]] |
| 5 | Vulnerability response / patching | [[L13 - Organizational Processes and Procedures]], [[L18 - Basic Network Security]] |
