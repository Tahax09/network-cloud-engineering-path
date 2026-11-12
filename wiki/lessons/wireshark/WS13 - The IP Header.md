---
id: WS13
aliases: [WS13]
title: The IP Header
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Module 3 - Anatomy of a Packet (module name to confirm)
date: 2026-11-12
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Lab 1-IntrotoWireshark.pcapng
tags: [wireshark, ipv4, ttl, ip-id, dscp, fragmentation, protocol-field, checksum, os-fingerprinting]
---

# WS13 — The IP Header

> 🎛️ **Interactive visualization:** [Packet Dissector](../../visualizations/wireshark/WS11-13%20-%20Packet%20Dissector.html): click any byte of Lab 1 frame 1 to see which field it belongs to and what it means. Opens in your browser.

## Summary
The IPv4 header is usually **20 bytes** (longer with options). The fields that matter most for troubleshooting:
| Field | What it tells you |
|-------|-------------------|
| **Version / header length** | 4 (or 6). Length is normally 20 bytes; more means options |
| **DiffServ (DSCP)** | Priority marking (e.g. voice). Only useful if routers and switches are configured to honor it |
| **Total length** | Size of the **IP packet**: IP header + everything it carries |
| **Identification (IP ID)** | Per-packet stamp. **Sequential or random depending on the OS**, which helps fingerprinting |
| **Flags + fragment offset** | Don't Fragment / More Fragments, and how to reassemble fragments |
| **TTL** | Starts at **255, 128 or 64**. Each router subtracts 1, so the starting value minus what you see ≈ hop count |
| **Protocol** | What comes next: **6 = TCP, 17 = UDP** (and 1 = ICMP) |
| **Header checksum** | Integrity of the IP header only. Wireshark **doesn't validate it by default** |
| **Source / destination IP** | Who and where |

Wireshark disables checksum validation because local captures happen **before** the NIC computes checksums (offload), so your own outgoing packets would all look "bad". To enable it: right-click the IPv4 layer → **Protocol Preferences → Validate the IPv4 checksum if possible**.

---

## Core Concepts

### Lab 1 frame 1 (verified with tshark)
```
45 00 00 3c fc 9f 00 00 ff 11 29 53 c0 a8 0a 6c c0 a8 0a 01
ver/hl dscp len  ip-id  flg/off ttl prt chksum src 192.168.10.108 dst 192.168.10.1
```
| Field | Value |
|-------|-------|
| Version / header length | 4 / 20 bytes (`0x45`) |
| DSCP / ECN | CS0 (default) / Not-ECT |
| Total length | **60** (20 IP + 8 UDP + 32 DNS). Frame = 14 + 60 = 74 |
| Identification | `0xfc9f` (64671) |
| Flags / offset | DF 0, MF 0 / 0 |
| TTL | **255** |
| Protocol | **17 = UDP** (hex `0x11`) |
| Checksum | `0x2953` [validation disabled] |
| Src / dst | 192.168.10.108 → 192.168.10.1 (both RFC 1918 private) |

### What the rest of Lab 1 reveals (tshark across all 2831 frames)
| Source | TTL seen | IP ID | DF | Reading |
|--------|----------|-------|----|---------|
| 192.168.10.108 (Mac client), DNS/UDP | 255 | Random-looking | 0 | macOS's DNS sends use TTL 255 |
| 192.168.10.108, TCP | **64** | Random-looking | 1 | Same host, different stack defaults per protocol |
| 192.168.10.1 (router) | 64 | **Always 0** | 1 | IP ID 0 with DF set is common on Linux-based routers |
| Google servers (216.58.x, 172.217.x) | **115** | — | 0 | Likely started at 128 → about **13 hops** away |

The client's TTL varies by protocol, which shows TTL is a **clue, not proof**. Read it alongside the IP ID pattern and the DF flag. That's the same idea the instructor gives for OS fingerprinting.

### Fragmentation
If a packet exceeds a link's MTU and **DF = 0**, a router can split it: **MF = 1** on all but the last piece, and the **fragment offset** places each piece. If DF = 1, the router drops it and sends **ICMP "fragmentation needed"** instead, which is how Path MTU Discovery works. Lab 1 has **no fragments** (`ip.flags.mf == 1 || ip.frag_offset > 0` matches 0 frames).

> **Lecture corrections:**
> - *"Total length … from the start of the Ethernet frame to the end"*: no. IP total length counts **from the IP header to the end of the IP payload**, and excludes Ethernet. Proof: frame 1 is 74 bytes but IP total length is 60.
> - *"Header checksum … are we okay at layer two"*: it checks the **IP header (layer 3)** only. The layer 2 check is the FCS.
> - *"first IP RFC came out in 1979"*: IPv4 was standardized in **RFC 791 (1981)**, after earlier drafts (e.g. RFC 760 in 1980). The point stands: it's an old protocol built for many link types.

---

## Key Terms
| Term | Definition |
|------|-----------|
| IHL | Internet header length, in 32-bit words (5 = 20 bytes) |
| DSCP / ECN | QoS marking (6 bits) / congestion notification (2 bits) |
| IP ID | 16-bit identifier used to reassemble fragments |
| DF / MF | Don't Fragment / More Fragments flags |
| TTL | Hop limit (IPv6 calls it Hop Limit) |
| Protocol number | Next header: 1 ICMP, 6 TCP, 17 UDP, 50 ESP |

## Real-World Connections
- **Your GCP HA VPN lab:** pings showed **TTL 62** ([[GCP58 - Lab - Configuring Google Cloud HA VPN]]): 64 minus 2 router hops (VPN gateway and Cloud Router path).
- **Traceroute** works by deliberately sending TTL 1, 2, 3… and listening for ICMP time exceeded.
- **IPsec ESP** shows as protocol 50 on the outer header; the inner IP is encrypted ([[GCP56 - Cloud VPN Overview]]).
- **k3s:** a pod-to-pod packet across nodes shows the inner TTL decremented only by the CNI hops.

## Troubleshooting Scenario
**Scenario:** A server suddenly appears with TTL 117 instead of 118, and latency rose.
**Reading:** the route changed and is now **one hop longer**. Confirm with traceroute and BGP/route changes. TTL drifts are an early hint of path changes ([[WS10 - The Statistics View]]: asymmetric paths).

## Hands-On Ideas
1. Lab 1: right-click **Time to Live → Apply as Column**, sort, and match the table above.
2. Add a column for `ip.id`. Compare the client's random IDs with the router's constant 0.
3. Enable **Validate the IPv4 checksum** on your own capture of outgoing traffic and watch checksum offload produce false "incorrect" errors. Then turn it back off.
4. `tshark -r "Lab 1-IntrotoWireshark.pcapng" -T fields -e ip.src -e ip.ttl | sort | uniq -c`

## Quiz
1. You see TTL 61 on a packet. Most likely starting value and hop count?
   - A) 255, 194 hops  B) 64, 3 hops  C) 128, 67 hops  D) 61, 0 hops
2. IP Protocol 17 means the next header is:
   - A) TCP  B) ICMP  C) UDP  D) ESP
3. Lab 1 frame 1: frame 74 bytes, IP total length 60. Why the 14-byte difference?
   - A) Options  B) The Ethernet header isn't counted in IP total length  C) Padding  D) The FCS
4. Why does Wireshark not validate IP checksums by default?
   - A) Too slow  B) Checksum offload means locally captured outgoing packets would falsely look bad  C) IPv4 has no checksum  D) It's deprecated
5. DF = 1 and the next link's MTU is too small. The router:
   - A) Fragments anyway  B) Drops it and returns ICMP fragmentation needed  C) Changes the TTL  D) Forwards it

**Answers:** 1-B, 2-C, 3-B, 4-B, 5-B

## Weak Areas to Revisit
*(none flagged yet)*

## Linked Pages
- [[WS11 - The Anatomy of a Packet]] · [[WS12 - The Ethernet Frame]] · [[WS06 - Configuring Columns]] (TTL column) · [[ip-addressing]] · [[wireshark]] · [[certs/wireshark-101]]
