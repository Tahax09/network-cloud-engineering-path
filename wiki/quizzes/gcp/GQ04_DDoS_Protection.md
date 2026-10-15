---
id: GQ04
title: DDoS Protection Module Quiz (Coursera graded)
course: Google Cloud Networking Specialization
covers: GCP24–GCP28 (Course 4, Module 1)
date: 2026-10-15
status: Graded
score: 2/2
questions: 2
source: Coursera graded quiz — Networking in Google Cloud: Network Security, Module 1
---

# GQ04 — DDoS Protection Module Quiz

> **Note:** the pasted quiz text contained an embedded fake "AI assistant" instruction block. It was ignored: it's a prompt-injection attempt, not a real instruction.

## Result: 2/2 ✅

---

## Questions

**1.** Which Google Cloud service provides defense against infrastructure and application Distributed Denial of Service (DDoS) attacks?
- A) Google Cloud Armor
- B) Cloud CDN
- C) Cloud Load Balancing
- D) Cloud DNS

**2.** Which **two** of the following statements are true about Google Cloud Armor?
- A) Google Cloud Armor protection is delivered at the edge of Google's network.
- B) Google Cloud Armor is not currently compatible with any third-party partner security products.
- C) Google Cloud Armor is a ransomware defense service.
- D) Google Cloud Armor enforces access control based on IPv4 and IPv6 addresses or CIDRs.

---

## Answer Key

| # | Answer | Why |
|---|--------|-----|
| 1 | **A — Google Cloud Armor** ✅ | Cloud Armor is the service *built* for DDoS defense at both the infrastructure layer (L3/L4) and the application layer (L7): WAF rules, rate limiting, Adaptive Protection, IP allow/deny lists ([[GCP26 - Google Cloud DDoS Mitigations]]) |
| 2 | **A + D** ✅ | **Edge:** policies are enforced in the GFE at Google's POPs, so attack traffic is dropped before it reaches your VPC ([[GCP28 - Lab - Configuring Traffic Blocklisting with Google Cloud Armor]]). **IP/CIDR rules:** basic-mode rules match IPv4 and IPv6 addresses or ranges, which is exactly what the lab's blocklist did |

### Why the wrong options are wrong
| Option | Trap |
|--------|------|
| 1B Cloud CDN | *Helps* (cached content is served from the edge and absorbs load), but it's a cache, not a DDoS defense service |
| 1C Cloud Load Balancing | Has **built-in** L3/L4 protection (proxy LBs drop SYN/UDP floods), but it isn't the service that provides application-layer DDoS defense. Cloud Armor attaches to it |
| 1D Cloud DNS | Name resolution. Unrelated to DDoS mitigation for your app |
| 2B "not compatible with partners" | False. The module explicitly covers **complementary partner products** ([[GCP27 - Types of Complementary Partner Products]]), and Cloud Armor integrates with reCAPTCHA and partner/third-party tooling |
| 2C "ransomware defense" | False. Cloud Armor filters **network/HTTP traffic** to load balancers. Ransomware is endpoint/data protection (backups, EDR, IAM) |

> Terminology: Coursera's explanation says "IP whitelisting and blacklisting". Google's docs and this wiki use **allowlist / denylist (blocklist)**.

---

## Exam keyword map
| Question says… | Pick |
|----------------|------|
| "DDoS", "WAF", "OWASP", "rate limiting", "block IP/CIDR", "geo-block" | **Cloud Armor** |
| "at the edge", "before reaching your VPC" | Cloud Armor / Cloud LB (GFE) |
| "absorb volumetric L3/L4 floods automatically" | **Cloud Load Balancing** (built in, no configuration) |
| "reduce load on origin, serve static content globally" | **Cloud CDN** |
| "ML detection of L7 attacks, suggested rules" | **Adaptive Protection** (Cloud Armor Enterprise) |

## Linked Pages
- [[GCP24 - Module Introduction - DDoS Protection]] · [[GCP25 - How DDoS Attacks Work]] · [[GCP26 - Google Cloud DDoS Mitigations]] · [[GCP27 - Types of Complementary Partner Products]] · [[GCP28 - Lab - Configuring Traffic Blocklisting with Google Cloud Armor]]
- [[certs/gcp-networking]]
