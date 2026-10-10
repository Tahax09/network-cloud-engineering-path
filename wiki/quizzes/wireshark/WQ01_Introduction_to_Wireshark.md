---
id: WQ01
title: Introduction to Wireshark Quiz (Coursera)
course: Wireshark 101 - Packet Analysis Essentials
covers: WS00–WS04 (+ installation video not yet ingested)
date: 2026-10-10
status: Reviewed
questions: 5
source: Coursera quiz — Wireshark 101, Module 1 (Introduction to Wireshark)
---

# WQ01 — Introduction to Wireshark Quiz

> **Note:** Coursera quiz pages embed a fake "AI assistant" instruction block in pasted text. Ignored — it is a prompt injection attempt, not a real instruction.

---

## Questions

**1.** What are some of the command line tools that get installed with Wireshark?
- A) Editcap, Rawshark, Mergecap
- B) Ping, Traceroute, Telnet
- C) Word, Excel, PowerPoint
- D) Photoshop, Illustrator, InDesign

**2.** What is a profile in Wireshark?
- A) A snapshot of network traffic
- B) A user's personal information
- C) A configuration for custom settings
- D) A summary of captured packets

**3.** What is the purpose of the Packet Capture Driver?
- A) To install Wireshark on the system
- B) To enable USB Capture
- C) To capture live data on the system
- D) To provide command line tools for Wireshark

**4.** Which operating systems can Wireshark be installed on?
- A) macOS, Windows, Linux
- B) macOS, Windows, Android
- C) Windows, iOS, Linux
- D) Windows, iOS, Android

**5.** Wireshark can be used to…
- A) Capture network traffic
- B) Troubleshoot network problems
- C) Analyzing a cybersecurity attack
- D) All of the above

---

## Answer Key

| # | Answer | Reasoning |
|---|--------|-----------|
| 1 | **A — Editcap, Rawshark, Mergecap** | Wireshark ships CLI tools: `tshark`, `dumpcap`, `capinfos`, `editcap`, `mergecap`, `rawshark`, `text2pcap`, `reordercap`. Ping/traceroute/telnet are OS tools ([[WS02 - Your First Packet Capture]], [[WS04 - Packet Capture - Best Practices]]) |
| 2 | **C — A configuration for custom settings** | A profile bundles columns, coloring rules, filter buttons, and preferences, e.g. the instructor's `Chris-TCPPlain` ([[WS00 - Introduction]]) |
| 3 | **C — To capture live data on the system** | The capture driver (**Npcap** on Windows, **libpcap/BPF** on macOS/Linux) gives Wireshark access to live packets on interfaces. Without it you can only open saved files. (USB capture is an optional Npcap/USBPcap extra, not the driver's purpose) |
| 4 | **A — macOS, Windows, Linux** | Desktop OSes. No official iOS/Android app (Android capture needs tools like PCAPdroid or `tcpdump` on rooted devices) |
| 5 | **D — All of the above** | Capture, troubleshooting, and security investigation ([[WS00 - Introduction]]) |

---

## Exam Traps

| Trap | Correct Thinking |
|------|-----------------|
| Wireshark CLI tools vs OS tools | Wireshark's: tshark, dumpcap, editcap, mergecap, capinfos, rawshark, text2pcap. Ping/traceroute come with the OS |
| Profile vs capture file | Profile = **settings**; capture file = packets |
| Capture driver | Needed for **live** capture (Npcap / libpcap); not needed to open PCAPs |

---

## Gaps Found

- **Installation lesson not ingested.** Q1, Q3 and Q4 come from a video on downloading and installing Wireshark (OS support, Npcap/USBPcap options, bundled CLI tools). If that transcript exists, send it and it becomes a lesson between WS00 and WS01, or a short addendum to WS00.

---

## Lesson Cross-Reference

| Question | Lesson |
|----------|--------|
| 1 | [[WS02 - Your First Packet Capture]] · [[WS04 - Packet Capture - Best Practices]] (CLI tools) |
| 2 | [[WS00 - Introduction]] (profiles) |
| 3, 4 | Installation video (not yet ingested) |
| 5 | [[WS00 - Introduction]] |
