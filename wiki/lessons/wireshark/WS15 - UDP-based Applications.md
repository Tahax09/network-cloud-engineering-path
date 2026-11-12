---
id: WS15
aliases: [WS15]
title: UDP-based Applications
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Module 3 - How to Interpret Network Traffic
date: 2026-11-12
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Lab 1-IntrotoWireshark.pcapng
tags: [wireshark, udp, dns, connectionless, best-effort, quic]
---

# WS15 — UDP-based Applications

## Summary
UDP is **connectionless** and **best effort**. The application hands its payload to the stack, UDP wraps it, and it's sent with no handshake, no ACKs and no retransmission. The header has just **4 fields (8 bytes)**:
| Field | Size |
|-------|------|
| Source port | 2 B |
| Destination port | 2 B |
| Length | 2 B: UDP header + payload |
| Checksum | 2 B |

Loss handling is the **application's** job. **DNS** retries the query itself if no answer arrives in time.

---

## Core Concepts

### Lab 1 frame 1 (DNS query)
| Field | Value |
|-------|-------|
| Ports | 63151 (ephemeral) → **53** (DNS) |
| Length | **40** = 8 UDP header + 32 DNS payload |
| Checksum | `0x8e1c` (not validated by default) |

### TCP vs UDP
| | TCP ([[WS14 - The TCP Header]]) | UDP |
|--|-----|-----|
| Connection | 3-way handshake | None |
| Reliability | Sequence/ACK, retransmission | None: the app decides |
| Header | 20–60 B | 8 B |
| Typical uses | Web (HTTP/1.1, HTTP/2), SSH, databases | DNS, DHCP, NTP, SNMP, VoIP/RTP, **QUIC/HTTP/3** |

"More UDP on the web" means **QUIC** (HTTP/3 over UDP 443). Reliability and encryption move into the application layer, so Wireshark shows mostly encrypted QUIC payloads.

---

## Key Terms
| Term | Definition |
|------|-----------|
| Datagram | A UDP unit: header + payload, sent independently |
| Best effort | No delivery guarantee at that layer |
| QUIC | Encrypted transport over UDP used by HTTP/3 |

## Real-World Connections
- **k3s:** CoreDNS answers on UDP 53. Dropped DNS shows as a client retrying the same query ID after a timeout.
- **GCP:** Cloud DNS, Cloud NAT and UDP: NAT keeps UDP mappings only for a timeout window (30 s by default for UDP).

## Troubleshooting Scenario
**Scenario:** A page takes exactly ~5 seconds before loading.
**Look at:** filter `dns` and check for the **same transaction ID sent twice** about 5 s apart. The first query was lost, and the resolver timeout is the delay.

## Hands-On Ideas
1. Filter `udp` in Lab 1 (14 frames, all DNS). Match each query to its response by transaction ID.
2. Add a column for `udp.length`. Check that it's always `udp payload + 8`.

## Quiz
1. How many bytes is the UDP header?
   - A) 4  B) 8  C) 20  D) 40
2. Who retransmits a lost DNS query?
   - A) UDP  B) IP  C) The DNS client application  D) Nobody, ever
3. UDP length 40 with an 8-byte header means a payload of:
   - A) 40  B) 32  C) 48  D) 20

**Answers:** 1-B, 2-C, 3-B

## Weak Areas to Revisit
*(none flagged yet)*

## Linked Pages
- [[WS14 - The TCP Header]] · [[WS11 - The Anatomy of a Packet]] · [[certs/wireshark-101]]
