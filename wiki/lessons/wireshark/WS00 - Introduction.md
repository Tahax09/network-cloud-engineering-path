---
id: WS00
aliases: [WS00]
title: Introduction
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Introduction
date: 2026-10-10
status: Complete
quiz_attempted: No
weak_areas: []
tags: [wireshark, packet-analysis, course-intro, chris-greer, lab-files, profiles]
---

# WS00 — Introduction

## Summary
This is the course opener for *Wireshark 101: Packet Analysis Essentials*. Packet analysis is a core skill for **network performance problems, application issues, and cybersecurity events** (forensics, malware analysis, threat hunting). It's overwhelming at first: you're capturing, analyzing, and interpreting traffic at high speed. The goal is to read trace files comfortably and turn them into **actionable results**. The course is built around hands-on labs. The guiding idea: **"Packets don't lie."**

---

## Core Concepts

### Why packet analysis
| Use | What packets give you |
|-----|----------------------|
| Network performance | Real latency, loss, retransmissions, not dashboard averages |
| Application issues | Exact request/response timing, which proves where delay lives ([[WS01 - Pre-Lesson - Your First Lab]]) |
| Cybersecurity | Network forensics, malware C2/beaconing, data exfiltration evidence |

Packets are the **ground truth**. Logs can be incomplete, misconfigured, or tampered with, but a capture shows what actually crossed the wire.

### The instructor
**Chris Greer**: independent protocol analyst specializing in network analysis and threat hunting, an instructor for **Wireshark University**, and a YouTube content creator. His term for a fluent packet analyst is a **"packet head"**.

### Course files
- Source: the instructor's **Packet Pioneer** GitHub repository, *Wireshark 101* (README + zip).
- Contents: the lab PCAPs plus a folder with two of his **Wireshark configuration profiles**.
- In this wiki: everything is stored in `raw/assets/wireshark-101/`:

| File | Lesson |
|------|--------|
| Pre-Lab-SlowNetwork.pcapng | [[WS01 - Pre-Lesson - Your First Lab]] |
| Lab 1 – Lab 7 `.pcapng` | Upcoming lessons |
| Profiles/Chris-TCPPlain.zip, Chris-Security.zip | Import via **Edit → Configuration Profiles → Import from zip** |

> **Note:** The video says "seven lab pcaps." The download has **eight**: the seven numbered labs plus `Pre-Lab-SlowNetwork.pcapng` used in the Pre-Lesson.

### What a Wireshark profile is
A **configuration profile** bundles columns, coloring rules, display filter buttons, decode-as entries, IO graph settings, and preferences. You switch profiles from the status bar (bottom right). Using the instructor's profiles makes your screen match the videos. Typically you keep separate profiles per task, e.g. a TCP-troubleshooting profile and a security profile.

---

## Key Terms

| Term | Definition |
|------|-----------|
| Packet analysis / protocol analysis | Capturing and interpreting network traffic to diagnose problems or investigate incidents |
| Trace file | A saved capture (`.pcap` / `.pcapng`) |
| Packet head | The instructor's term for a fluent packet analyst |
| Configuration profile | Named Wireshark settings bundle (columns, colors, filter buttons, preferences) |
| Network forensics | Reconstructing events from captured traffic |

---

## Real-World Connections
- **Network+:** Protocol analyzers and packet capture are objective 5.5 ([[L25 - Troubleshooting Tools]]) and 3.2 ([[L14 - Network Monitoring]]). This course turns those exam bullet points into practical skill.
- **Security path (AZ-500 → KCSA → CKS):** Packet-level evidence underpins incident response. CKS runtime security (Falco alerts → "what did that pod actually send?") is much easier when you can read a capture.
- **k3s lab:** Kubeshark is effectively Wireshark for the cluster. Profiles map to Kubeshark's saved filters and views.

---

## Troubleshooting Scenario
**Scenario:** A teammate opens a course PCAP, but their columns and colors don't match the instructor's screen, so following along is confusing.
**Fix:** Import `Chris-TCPPlain.zip` as a configuration profile and switch to it (status bar → Profile). Keep the *Default* profile untouched so you can always compare against stock settings.

---

## Hands-On Ideas
1. Import both instructor profiles and toggle between them on `Pre-Lab-SlowNetwork.pcapng`. Note which columns and coloring rules each adds.
2. Create your own profile, `Tah-k3s`, with columns for `tcp.stream`, delta time displayed, and `http.host`, for your cluster captures.
3. Run `capinfos raw/assets/wireshark-101/*.pcapng` to see packet counts and durations for every lab before you start.

---

## Quiz

1. What are the three main areas where the course says packet analysis helps?
   - A) Cabling, wireless, DNS  B) Network performance, application issues, cybersecurity events  C) Routing, switching, NAT  D) Billing, licensing, compliance

2. What does "packets don't lie" mean in practice?
   - A) Packets are always encrypted  B) Captures show what actually crossed the wire, independent of logs or assumptions  C) Packets can't be dropped  D) Wireshark fixes problems automatically

3. What is a Wireshark configuration profile?
   - A) A capture file  B) A named bundle of columns, coloring rules, filter buttons, and preferences  C) A user account  D) A network interface

4. How do you load the instructor's profiles?
   - A) File → Open  B) Edit → Configuration Profiles → Import from zip  C) Capture → Options  D) Statistics → Conversations

**Answers:** 1-B, 2-B, 3-B, 4-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[WS01 - Pre-Lesson - Your First Lab]] — First lab: client vs network vs server
- [[wireshark]] — Wireshark reference page
- [[wireshark-101]] — Course progress
- [[L25 - Troubleshooting Tools]] · [[L14 - Network Monitoring]]
