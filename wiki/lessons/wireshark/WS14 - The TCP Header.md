---
id: WS14
aliases: [WS14]
title: The TCP Header
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Module 3 - How to Interpret Network Traffic
date: 2026-11-12
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Lab 1-IntrotoWireshark.pcapng
tags: [wireshark, tcp, ports, sequence-number, ack, header-length, flags, window, options, stream-index, segment-length]
---

# WS14 — The TCP Header

## Summary
TCP is **connection-oriented** and uses **ports** (software sockets on each endpoint). Its key header fields:
| Field | Size | Meaning |
|-------|------|---------|
| Source port | 2 B | Usually a high **ephemeral** port on the client |
| Destination port | 2 B | The **service** being hit (443 HTTPS, 21 FTP, 143 IMAP, 1433 SQL…) |
| Sequence number | 4 B | Where *my* byte count starts; a random **initial sequence number (ISN)** is sent in the SYN |
| Acknowledgment number | 4 B | Next byte I expect from *you* |
| Header length | 4 bits | **20 bytes** with no options, up to **60** with options |
| Flags | — | SYN, ACK, FIN, RST, PSH, URG… (the SYN coloring rule from [[WS07 - Coloring Traffic]]) |
| Window size | 2 B | **Receive window**: how much unacknowledged data I can buffer |
| Options | 0–40 B | MSS, window scale, SACK permitted, timestamps. Mostly negotiated **in the SYN only** |

**Wireshark-derived fields appear in [brackets]**, and they aren't on the wire:
| Field | Meaning |
|-------|---------|
| `[Stream index]` | Conversation number per 4-tuple (two IPs + two ports), starting at 0 |
| `[Conversation completeness]` | Did the capture see the open, data and close? |
| `[TCP Segment Len]` | **Payload bytes** in this segment: 0 = empty ACK. The instructor replaces the Length column with this |
| Relative sequence number | Wireshark shows the raw ISN as **0** so you can count bytes easily; the raw value is shown alongside |

---

## Core Concepts

### Lab 1 frame 3: the first SYN (verified with tshark)
| Field | Value |
|-------|-------|
| Ports | 60245 → **443** (to 172.217.8.68, Google) |
| `[Stream index]` | 0 (Lab 1 has streams 0–8) |
| `[TCP Segment Len]` | 0: a SYN carries no data |
| Sequence | Relative **0**, raw **1123826566** |
| Ack | 0 (no ACK flag yet) |
| Header length | **44** = 20 core + 24 bytes of options |
| Window | 65535 (scaled later by the window scale option) |
| Options | MSS **1460**, window scale shift **5** (×32), SACK permitted, timestamps |
| Frame length | 78 bytes, but segment length 0, which is why the Length column misleads |

### Make segment length a column
Right-click `[TCP Segment Len]` → **Apply as Column**. Then right-click the **Length** column → remove it. Data-carrying packets stand out from empty ACKs at a glance.

> **Lecture correction:** TCP's base spec is **RFC 793** (1981), not "RFC 7093". It's now consolidated in **RFC 9293** (2022). Ports range **0–65535** (0 is reserved and not used in practice).

---

## Key Terms
| Term | Definition |
|------|-----------|
| ISN | Initial sequence number, randomly chosen in the SYN |
| 4-tuple | Src IP, dst IP, src port, dst port: identifies a TCP conversation |
| MSS | Maximum segment size: largest payload the sender will accept (1460 = 1500 MTU − 40) |
| Window scale | SYN option multiplying the 16-bit window (shift 5 = ×32) |
| SACK | Selective acknowledgment: acknowledge non-contiguous blocks |

## Real-World Connections
- **GCP:** VPC MTU 1460 means VM SYNs advertise MSS **1420**. Seeing 1460 from a cloud VM suggests MSS clamping somewhere.
- **k3s:** Kubeshark groups traffic by the same 4-tuple; `tcp.stream eq N` is the Wireshark equivalent of following one connection.

## Troubleshooting Scenario
**Scenario:** A large file transfer stalls, but small requests work.
**Look at:** the SYN options. If both sides advertise MSS 1460 but the path has a smaller MTU (a VPN or tunnel), full-size segments get dropped. That's an MSS/MTU mismatch: clamp the MSS on the tunnel.

## Hands-On Ideas
1. Lab 1 frame 3 → Follow → TCP Stream, then filter `tcp.stream eq 0`.
2. Add columns for `tcp.len`, `tcp.window_size` and `tcp.flags.str`.
3. `tshark -r "Lab 1-IntrotoWireshark.pcapng" -Y "tcp.flags.syn==1 && tcp.flags.ack==0" -T fields -e tcp.options.mss_val`

## Quiz
1. TCP header length 44 bytes means:
   - A) 44 bytes of payload  B) 20 bytes core + 24 bytes of options  C) An error  D) IPv6
2. Which field is Wireshark-derived, not part of TCP?
   - A) Window size  B) `[Stream index]`  C) Sequence number  D) Destination port
3. Why does the SYN show sequence number 0?
   - A) The ISN is 0  B) Wireshark shows a relative number; the raw ISN is large and random  C) An error  D) No data
4. A packet with `[TCP Segment Len] = 0` and the ACK flag set is:
   - A) Carrying data  B) An empty ACK  C) A retransmission  D) A reset

**Answers:** 1-B, 2-B, 3-B, 4-B

## Weak Areas to Revisit
*(none flagged yet)*

## Linked Pages
- [[WS13 - The IP Header]] · [[WS15 - UDP-based Applications]] · [[WS07 - Coloring Traffic]] · [[certs/wireshark-101]]
