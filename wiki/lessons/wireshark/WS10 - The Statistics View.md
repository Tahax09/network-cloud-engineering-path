---
id: WS10
aliases: [WS10]
title: The Statistics View
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Module 2 - The Wireshark Interface
date: 2026-10-10
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Pre-Lab-SlowNetwork.pcapng
tags: [wireshark, statistics, conversations, endpoints, protocol-hierarchy, geoip, mac-address, asymmetric-path, tshark-z]
---

# WS10 — The Statistics View

## Summary
**Statistics** is the menu the instructor opens on **almost every PCAP**, before reading a single packet. It's the orientation step. This lesson (the end of Module 2, *The Wireshark Interface*) covers **Statistics → Conversations**, who talked to whom per layer (Ethernet, IPv4/6, TCP, UDP) with packets/bytes each way, **relative start** and **duration**, plus a timeline bar. It also covers **Statistics → Endpoints**, every unique MAC, IP, or IP:port, with GeoIP location to come later in the course. Uncheck the protocol tabs you never see (IPX, FDDI, Token Ring, IPv6 if unused) so the dialog stays readable.

---

## Core Concepts

### Statistics → Conversations
A **conversation** is traffic between **two endpoints at a given layer**. Tabs per layer:
| Tab | Endpoint pair | Note |
|-----|--------------|------|
| **Ethernet** | MAC ↔ MAC | One per L2 hop pairing seen at the capture point |
| **IPv4 / IPv6** | IP ↔ IP | Two hosts, regardless of ports |
| **TCP / UDP** | IP:port ↔ IP:port | Every **connection**. One IP conversation can carry **thousands** of TCP conversations |

**Columns that matter:**
| Column | Tells you |
|--------|-----------|
| Packets/Bytes **A → B** and **B → A** | Direction of the data (a large download vs a small request) |
| **Rel Start** | How far into the capture the conversation began |
| **Duration** | How long it lasted. Long-running vs many short connections |
| Timeline bar (graphical) | Visual start/length of each conversation across the capture |

**Patterns to recognize (from the lecture):** long-running vs bursty short connections, and an app that only ever uses **two connections at a time**, so each new one has to **wait for an old one to time out**. That serialization looks like slowness (compare the serialized requests in [[WS01 - Pre-Lesson - Your First Lab]]).

### Statistics → Endpoints
A per-layer list of **unique addresses** (MACs, IPs, TCP/UDP IP:port) with Tx/Rx packets and bytes. Questions it answers:
- How many unique **MACs/IPs** are active? (An unexpected one could be a rogue device or a scan.)
- Who are the **top talkers**? (Sort by Bytes.)
- **GeoIP** (later in the course): with MaxMind GeoLite2 databases configured, IPv4 endpoints show **country, city, ASN**. The Cybersec profile's country rules depend on this ([[WS07 - Coloring Traffic]]).

### Clean up the protocol list
Bottom left of the Conversations/Endpoints dialog: **Conversation Types / Endpoint Types** checkboxes. Untick protocols that never appear in your environment (**IPX, FDDI, Token Ring**, IPv6 if unused). The choice is saved in the **profile** ([[WS08 - Creating Profiles]]).

> **Caption note:** "FIDI" = **FDDI** (Fiber Distributed Data Interface, a legacy ring LAN).

---

## Pre-Lab File: What Statistics Shows (verified with tshark)

| Layer | Conversations | Details |
|-------|--------------|---------|
| **TCP** | **1** | `172.16.0.13:61330 ↔ 10.0.0.100:80`, 123 frames, 78 kB, start 0.000 s, duration **156.5 s** |
| **IPv4** | **1** | `172.16.0.13 ↔ 10.0.0.100`: client sent 55 frames / 24 kB, server sent **68 frames / 53 kB** (the download direction) |
| **Ethernet** | **2** | `client ↔ f2:b0:cd:65:7a:6f`: 55 frames, **client → only**. `f2:fb:fa:c6:c2:78 ↔ client`: 68 frames, **→ client only** |

**Why two Ethernet conversations for one IP conversation?** At the client, every packet is addressed by MAC to the **next hop**, not to the server (MACs are rewritten at every router, [[L01 - The OSI Model]], [[L07 - IP Addressing]]). Here:
- **Outbound** frames go to router MAC `f2:b0:cd…` (the default gateway).
- **Inbound** frames come from a **different** MAC, `f2:fb:fa…`.

So **traffic leaves via one L3 device and returns via another**: **asymmetric routing**, or a first-hop redundancy pair (HSRP/VRRP) where the active gateway for outbound differs from the return path ([[L09 - Routing Technologies]]). That matters when you place a capture point: a SPAN on only one router would see only half of the conversation ([[WS03 - Where to Capture on the Network]]).

> **Caveat:** all three MACs begin with **`f2`**. `0xf2` has the **locally-administered bit** set (second-lowest bit of the first octet), so these aren't vendor-assigned OUIs. The trace was most likely **anonymized** (e.g., with TraceWrangler) before distribution. The *pattern* (two next-hop MACs) is still meaningful; vendor lookups won't be.

**Protocol Hierarchy** (Statistics → Protocol Hierarchy) adds: `eth → ip → tcp` = 123 frames; **http** = 78 frames, of which 51 are marked `_ws.short` (truncated by the 96-byte snaplen, [[WS01 - Pre-Lesson - Your First Lab]]).

---

## Other Statistics Worth Knowing (beyond this lesson)
| Menu item | Use |
|-----------|-----|
| **Capture File Properties** | Duration, packet count, snaplen, interfaces, comments (the first click on a new PCAP) |
| **Protocol Hierarchy** | Protocol mix by % of frames/bytes; spot unexpected protocols |
| **I/O Graphs** | Throughput or packet rate over time; overlay filters (e.g., `tcp.analysis.retransmission`) |
| **Flow Graph** | Ladder diagram of a conversation (handshake, gaps) |
| **HTTP / DNS / TCP Stream Graphs** | Per-protocol request/response stats; TCP throughput, RTT and window graphs |
| **Resolved Addresses** | Names Wireshark resolved / saw in DNS |

### CLI equivalents (`tshark -q -z`)
```bash
tshark -r file.pcapng -q -z conv,tcp          # TCP conversations (also conv,ip / conv,eth / conv,udp)
tshark -r file.pcapng -q -z endpoints,ip      # endpoints per layer
tshark -r file.pcapng -q -z io,phs            # protocol hierarchy
tshark -r file.pcapng -q -z io,stat,1         # packets/bytes per 1 s interval
capinfos file.pcapng                          # Capture File Properties
```

---

## Key Terms

| Term | Definition |
|------|-----------|
| Conversation | Traffic between two endpoints at one layer (MAC, IP, or IP:port pair) |
| Endpoint | A single address at one layer (MAC, IP, IP:port) |
| Rel Start / Duration | When a conversation began relative to the first packet, and how long it lasted |
| A → B / B → A | Per-direction packet and byte counts |
| Top talker | Endpoint with the most bytes/packets |
| GeoIP | Mapping IPs to country/city/ASN using MaxMind databases |
| Protocol Hierarchy | Tree of protocols with frame/byte share |
| Locally administered MAC | MAC with bit 0x02 of the first octet set; not vendor-assigned (VMs, randomization, anonymization) |
| Asymmetric routing | Forward and return traffic take different paths/next hops |

---

## Real-World Connections
- **Network+:** Conversations/Endpoints is the per-capture version of **NetFlow top talkers** ([[L14 - Network Monitoring]]). Two next-hop MACs for one flow is a live example of **FHRP / asymmetric routing** ([[L09 - Routing Technologies]]), and of why stateful firewalls break when return traffic takes another path.
- **SOC:** The Endpoints IPv4 tab with GeoIP and a sort by bytes is a 30-second exfiltration check: an unexpected country or ASN with high outbound bytes.
- **k3s lab:** On a node capture, Conversations → TCP immediately shows how many pod connections hit Elasticsearch `:9200` and how long each lasted. Many short connections means no connection pooling. Ethernet conversations on `cni0` map pod veth MACs.
- **Cloud:** VPC Flow Logs are essentially an **IPv4/TCP Conversations table** without payload, so you can do the same "who talked to whom, how much, how long" analysis at cloud scale.

---

## Troubleshooting Scenario
**Scenario:** Users say a reporting app is slow. A 5-minute capture on the app server has 40,000 packets, too many to scroll.
**Approach (Statistics first):**
1. **Capture File Properties:** 5 min, no drops.
2. **Conversations → TCP**, sorted by **Rel Start**: the app opens connections to the database **two at a time**, and each new one starts only after an older one ends about 30 s later. That's a **connection-pool limit / timeout** pattern, visible in seconds.
3. **Endpoints → IPv4**: one unexpected IP, which turns out to be a monitoring agent. Excluded.
4. Only now, filter on one DB conversation (`tcp.stream == N`) and read packets.
**Fix:** raise the app's DB connection pool size and shorten the idle timeout. Verify with Conversations: overlapping, short-lived connections.

---

## Hands-On Ideas
1. On the Pre-Lab file: **Statistics → Conversations**. Check the Ethernet (2), IPv4 (1), and TCP (1) tabs and match the table above. Untick IPv6, IPX, FDDI and Token Ring.
2. **Statistics → Endpoints → Ethernet**: confirm `f2:fb:fa…` only transmits and `f2:b0:cd…` only receives.
3. Right-click the TCP conversation → **Apply as Filter → Selected → A↔B**. That builds a conversation filter for you.
4. Open a busier lab (`Lab 1-IntrotoWireshark.pcapng`, 2.6 MB) and compare the conversation count and the duration timeline.
5. CLI: `tshark -r Pre-Lab-SlowNetwork.pcapng -q -z conv,eth -z conv,ip -z conv,tcp -z io,phs`.
6. Check MAC randomization on your Mac: `ifconfig en0 | grep ether`. Is the first octet's 0x02 bit set (private Wi-Fi address)?

---

## Quiz

1. What does Statistics → Conversations show?
   - A) Traffic between pairs of endpoints per layer, with packets/bytes each way, start time and duration  B) Every packet's payload  C) Coloring rules  D) Capture filters

2. One IP conversation contains many TCP conversations. What does that mean?
   - A) Duplicate packets  B) Two hosts opened multiple connections (different port pairs)  C) A routing loop  D) IPv6 is in use

3. What do the Rel Start and Duration columns help you find?
   - A) MAC vendors  B) DNS names  C) When conversations began and how long they lasted, e.g. long-lived vs short bursty connections  D) TTL values

4. What does Statistics → Endpoints list?
   - A) Pairs of hosts  B) Unique addresses per layer (MAC, IP, IP:port) with Tx/Rx counts  C) Display filters  D) Interfaces

5. Why untick protocols such as IPX, FDDI, and Token Ring in the Conversations dialog?
   - A) They're insecure  B) They're not present on modern networks, and removing them declutters the view  C) It speeds up capture  D) It's required for GeoIP

6. The Pre-Lab file has one IPv4 conversation but two Ethernet conversations. What's the best explanation?
   - A) Duplicate client  B) ARP spoofing confirmed  C) A Wireshark bug  D) Outbound frames go to one next-hop MAC and replies arrive from a different router MAC (asymmetric path / FHRP)

7. All MACs in a trace start with `f2:`. What does that suggest?
   - A) Cisco devices  B) Locally administered (non-OUI) addresses, likely anonymized or randomized  C) Multicast  D) Broadcast

8. Which tshark option prints TCP conversation statistics?
   - A) `-q -z conv,tcp`  B) `-Y conv`  C) `-T conv`  D) `-c conv`

**Answers:** 1-A, 2-B, 3-C, 4-B, 5-B, 6-D, 7-B, 8-A

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[WS01 - Pre-Lesson - Your First Lab]] — The same trace, packet by packet
- [[WS03 - Where to Capture on the Network]] — Why asymmetric paths matter for capture placement
- [[WS08 - Creating Profiles]] — Settings saved per profile
- [[wireshark]] — Wireshark reference · [[wireshark-101]] — Course progress
- [[L14 - Network Monitoring]] · [[L09 - Routing Technologies]] · [[L07 - IP Addressing]]
