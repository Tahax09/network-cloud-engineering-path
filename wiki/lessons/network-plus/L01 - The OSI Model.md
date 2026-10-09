---
id: L01
aliases: [L01]
title: The OSI Model
course: CompTIA Network+
cert_track: network-plus
objective: "1.1 — Explain Concepts Related to the OSI Reference Model"
date: 2025-05-28
status: Complete
quiz_attempted: Yes
weak_areas: []
tags: [osi, layers, encapsulation, tcp, udp, mac, ip, pdu]
---

# L01 — The OSI Model

## Summary
The OSI model is a 7-layer conceptual framework describing how data travels between machines. TCP/IP does the real work; OSI provides the diagnostic language every engineer uses. "Layer 3 problem" = routing/IP. "Layer 2 problem" = MAC/switching. Master this map and you can locate any network fault.

## Core Concepts

### The 7 Layers

| Layer | Name | PDU | Address | Key Devices |
|-------|------|-----|---------|-------------|
| 7 | Application | Data | — | NGFW, IDS/IPS |
| 6 | Presentation | Data | — | Host (software) |
| 5 | Session | Data | — | Host (software) |
| 4 | Transport | Segment (TCP) / Datagram (UDP) | Port | Firewall, IDS/IPS |
| 3 | Network | Packet | IP Address | Router, L3 Switch |
| 2 | Data Link | Frame | MAC Address | L2 Switch, NIC |
| 1 | Physical | Bits | — | Hub, Repeater, Cable |

**Mnemonic (top→bottom):** All People Seem To Need Data Processing
**Mnemonic (bottom→top):** Please Do Not Throw Sausage Pizza Away

### Encapsulation — The Russian Doll Model
Sender goes **DOWN** the layers (wrapping). Receiver goes **UP** (unwrapping).

```
L7  [ DATA (HTTP GET) ]
L4  [ TCP Header | DATA ]                          ← SEGMENT
L3  [ IP Header | TCP Header | DATA ]              ← PACKET
L2  [ ETH Hdr | IP Hdr | TCP Hdr | DATA | ETH Tr ] ← FRAME
L1  101001011010...                                ← BITS
```

Each header is read and stripped by the matching layer on the receiving end.

### TCP vs UDP (Layer 4)

| | TCP | UDP |
|-|-----|-----|
| Connection | 3-way handshake (SYN→SYN-ACK→ACK) | Connectionless |
| Reliability | Acknowledgments, retransmission, ordering | Fire and forget |
| Speed | Slower (overhead) | Faster |
| PDU | Segment | Datagram |
| Use cases | HTTP, SSH, email | DNS, VoIP, video, gaming |

**Exam trap:** "Connectionless" on the exam means UDP (Layer 4), not IP (which is also connectionless but not called that on exams).

### Layer 2 — Key Insight
Even sending to a server in Tokyo, your frame's destination MAC = **your default gateway**, not Tokyo's server. Layer 2 is purely local. The router strips the frame, reads the IP packet, and re-frames for the next hop.

### Promiscuous Mode
Your NIC normally drops frames not addressed to it. In promiscuous mode it accepts all frames — how Wireshark and Kubeshark capture traffic.

## Key Terms

| Term | Definition |
|------|------------|
| PDU | Protocol Data Unit — data at a specific layer |
| Encapsulation | Wrapping data with headers going down the stack |
| Decapsulation | Removing headers going up the stack |
| Segment | TCP PDU (Layer 4) |
| Datagram | UDP PDU (Layer 4) |
| Packet | Layer 3 PDU |
| Frame | Layer 2 PDU |
| MAC Address | 48-bit hardware address, 12 hex digits (0–9, A–F only) |
| Default Gateway | Router your machine sends non-local traffic to |
| Promiscuous Mode | NIC accepts all frames regardless of destination MAC |
| DPI | Deep Packet Inspection — L7 analysis of payload content |
| Connection-oriented | TCP — establishes session before sending |

## Real-World Connections

**Visiting https://google.com:**
- L7: Browser builds HTTP GET
- L6: TLS encrypts the data
- L5: New session created
- L4: TCP wrap, src port 52341 → dst port 443
- L3: IP packet, your IP → Google's IP
- L2: Frame, your MAC → **your router's MAC** (not Google's)
- L1: Bits out your Wi-Fi card

**Your k3s cluster:** Kubeshark operates in promiscuous mode on the pod network — it's capturing at L2/L1 and reassembling up through L7. Every Kubeshark view maps to an OSI layer.

## Troubleshooting Scenario
> "Users can ping the server by IP but can't access the website."

- L1–L3 ✅ (ping = ICMP = L3 works, implies L1/L2 ok)
- L4 ❓ → `telnet server 443` — firewall blocking port? Service down?
- L7 ❓ → if port open but site fails → web server config? SSL cert? App crash?

**Pattern:** Bottom-up or top-down — systematically eliminate layers.

## Hands-On Practice
```bash
# Capture traffic and identify layer headers
sudo tcpdump -i eth0 -w capture.pcap

# Layer 3 — routing table
ip route show
traceroute google.com

# Layer 2 — ARP table (MAC to IP mappings on LAN)
arp -n

# Layer 4 — open ports and services
ss -tulnp

# See all layers in a curl request (-v shows TLS, TCP connect, HTTP headers)
curl -v https://example.com
```

## Quiz Questions
1. What is the correct PDU name for data sent via UDP at Layer 4?
   - a) Segment b) Packet **c) Datagram** d) Frame

2. A user can't reach a remote server. Ping succeeds. `telnet server 22` fails. Which layer do you investigate and why?

3. Your laptop sends email to Germany. What MAC address is in the destination field of the outgoing frame?
   - a) Email server's MAC **b) Default gateway's MAC** c) DNS server's MAC d) Broadcast MAC

4. Which is a connectionless protocol? a) TCP b) HTTP **c) UDP** d) All Layer 3 protocols

5. In k3s, pod A sends HTTP to pod B on a different node. At Layer 2 — whose MACs appear in the frame? *(Hint: what sits between pods on different nodes?)*

## Weak Areas to Revisit
*(none flagged yet)*

## Related
- [[concepts/osi-model]] — full concept reference
- [[L02 - Networking Appliances & Applications]] — devices at each OSI layer
- [[labs/k3s-lab]] — promiscuous mode, Kubeshark
