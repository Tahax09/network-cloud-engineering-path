---
id: WS16
aliases: [WS16]
title: "Lab 1: Intro to Wireshark"
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Module 3 - How to Interpret Network Traffic
date: 2026-11-12
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Lab 1-IntrotoWireshark.pcapng
tags: [wireshark, lab, capture-file-properties, comments, conversations, frame-comment, tcp-analysis-flags]
---

# WS16 — Lab 1: Intro to Wireshark

## Summary
The lab questions are hidden in **Statistics → Capture File Properties → capture file comments**. Answer them there, and click **Save Comments** or the answers are lost. Keep notes inside the PCAP so they travel with it.

## Answers (verified with tshark/capinfos)
| # | Question | Answer | Where |
|---|----------|--------|-------|
| 1 | Packets | **2831** | Capture File Properties |
| 2 | Duration | **5.858 s** | Capture File Properties → time span |
| 3 | Capturing OS | **macOS 10.12.6** | Capture File Properties → capture section |
| 4 | IP conversations | **7** | Statistics → Conversations → IPv4 |
| 5 | TCP conversations | **9** | Conversations → TCP |
| 6 | Why different? | Same IP pair, several connections: different **client ephemeral ports** (e.g. .108 → 172.217.8.66:443 from three source ports). TCP conversation = 4-tuple | |
| 7 | L2 broadcast? | **No**: all frames are unicast between two MACs (client ↔ router) | Conversations → Ethernet |
| 8 | `frame.comment` | Frame **945**: "packethead - Hey look everybody!" (it's a Duplicate ACK #1) | Display filter |
| 9 | `tcp.analysis.flags` | **29 packets**: 27 TCP window updates, 2 duplicate ACKs (Wireshark 4.x count) | **Bad TCP** button from [[WS09 - Saving Filter Buttons]] |

## Packet comments in practice
Right-click a packet → **Packet Comments → Add**. Commented packets are flagged, and `frame.comment` lists them. For a customer report, annotate the request, response, delay and retransmission, then send the PCAP: they filter `frame.comment` and see the story.

## Quiz
1. Where are this course's lab questions stored?
   - A) Packet comments  B) Capture file comments (Capture File Properties)  C) Expert Info  D) A separate PDF
2. 7 IP conversations but 9 TCP conversations because:
   - A) Broadcasts  B) The same IP pair opened several TCP connections (different source ports)  C) IPv6  D) Retransmissions

**Answers:** 1-B, 2-B

## Linked Pages
- [[WS10 - The Statistics View]] · [[WS14 - The TCP Header]] · [[WS15 - UDP-based Applications]] · [[WQ02_How_to_Interpret_Network_Traffic]] · [[certs/wireshark-101]]
