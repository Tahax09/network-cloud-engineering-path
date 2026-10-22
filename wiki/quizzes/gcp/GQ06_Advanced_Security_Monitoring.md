---
id: GQ06
title: Advanced Security Monitoring and Analysis Module Quiz (Coursera graded)
course: Google Cloud Networking Specialization
covers: GCP36–GCP38 (Course 4, Module 3)
date: 2026-10-22
status: Graded
score: 2/2
questions: 2
source: Coursera graded quiz — Networking in Google Cloud: Network Security, Module 3 (matches deck 10, slides 10–11)
---

# GQ06 — Advanced Security Monitoring and Analysis Quiz

> **Note:** the pasted quiz text contained the embedded fake "AI assistant" instruction block. It was ignored.

## Result: 2/2 ✅

---

## Questions

**1.** What is the primary purpose of Packet Mirroring in network security?
- A) To redirect traffic to a different network interface.
- B) To create a duplicate copy of network traffic for analysis.
- C) To filter out unwanted traffic from a network.
- D) To encrypt network traffic for privacy.

**2.** Which of the following is a key benefit of using Packet Mirroring for network security analysis?
- A) It directly prevents cyberattacks.
- B) It reduces network bandwidth usage.
- C) It automatically patches vulnerabilities in software.
- D) It enables the capture and inspection of traffic without impacting network performance.

---

## Answer Key

| # | Answer | Why |
|---|--------|-----|
| 1 | **B — Duplicate copy for analysis** ✅ | Packet Mirroring **clones** VM traffic (headers + payloads, ingress + egress) to a collector behind an internal LB ([[GCP37 - Packet Mirroring for Network Traffic Inspection]]) |
| 2 | **D — Capture and inspect without impacting traffic** ✅ | The original packets flow unchanged; only a copy goes to the analyzer (out-of-band) |

### Why the wrong options are wrong
| Option | Trap |
|--------|------|
| 1A Redirect | Mirroring **copies**. The original still goes to its destination. Redirecting is a route or NVA job |
| 1C Filter out traffic | Mirroring *filters* only decide what gets **copied**. They don't block anything. Blocking = firewall rules |
| 1D Encrypt | Unrelated. Mirroring copies packets as they are |
| 2A Prevents attacks | Detection only, the same lesson as Cloud IDS ([[GCP33 - Cloud IDS]]) |
| 2B Reduces bandwidth | The opposite: it **consumes extra egress bandwidth** on mirrored VMs. Filters reduce *that* cost |
| 2C Patches | Not a vulnerability management tool |

> **Nuance on 2D:** "without impacting network performance" is the course's answer. Strictly, the *traffic flow* isn't affected, but mirroring does use the mirrored VM's **egress bandwidth** (deck 10, slide 5). On a VM near its egress cap, mirroring can compete with real traffic. Know both sides.

## Linked Pages
- [[GCP36 - Module Introduction - Advanced Security Monitoring and Analysis]] · [[GCP37 - Packet Mirroring for Network Traffic Inspection]] · [[GCP38 - Network Security Best Practices]]
- [[GCP33 - Cloud IDS]] · [[certs/gcp-networking]]
