# OSI Model

## Definition
A 7-layer conceptual framework describing how data travels between networked devices. Not a real protocol — it's the diagnostic language of networking. TCP/IP is what actually runs.

## The 7 Layers

| Layer | Name | PDU | Address | Role |
|-------|------|-----|---------|------|
| 7 | Application | Data | — | User-facing protocols (HTTP, DNS, SSH, SMTP) |
| 6 | Presentation | Data | — | Encoding, encryption (TLS), compression |
| 5 | Session | Data | — | Manages parallel conversations (sessions) |
| 4 | Transport | Segment / Datagram | Port | Reliable (TCP) or fast (UDP) delivery |
| 3 | Network | Packet | IP Address | Routing across networks |
| 2 | Data Link | Frame | MAC Address | Local delivery within a network |
| 1 | Physical | Bits | — | Signals: electrical, optical, radio |

## Encapsulation Flow
Sender: L7 → L1 (add headers at each layer)
Receiver: L1 → L7 (strip headers at each layer)

## TCP vs UDP at Layer 4

| Property | TCP | UDP |
|----------|-----|-----|
| Setup | 3-way handshake | None |
| Reliability | Acks, retransmission, ordering | None |
| Speed | Slower | Faster |
| PDU name | Segment | Datagram |
| Use cases | HTTP/S, SSH, email | DNS, VoIP, video, gaming |

## Key Rule: Layer 2 Is Always Local
Regardless of how far data travels, the L2 frame always addresses the **next-hop MAC** — not the final destination. Each router strips and re-frames for the next segment.

## OSI vs TCP/IP Mapping
```
OSI                  TCP/IP
7 Application   ─┐
6 Presentation  ─┤  → Application
5 Session       ─┘
4 Transport     ───  → Transport
3 Network       ───  → Internet
2 Data Link     ─┐
1 Physical      ─┘  → Link
```

## CLI Tools by Layer

| Layer | Tool | What It Shows |
|-------|------|---------------|
| L7 | `curl -v`, browser DevTools | HTTP headers, TLS |
| L4 | `ss -tulnp`, `telnet host port` | Open ports, TCP state |
| L3 | `ip route`, `traceroute`, `ping` | Routing, hops, ICMP |
| L2 | `arp -n`, `ip neigh` | MAC-to-IP mappings |
| L1–L2 | `tcpdump`, Wireshark, Kubeshark | Raw frames and up |

## Linked Lessons
- [[L01 - The OSI Model]] — full lesson on OSI model
- [[L02 - Networking Appliances & Applications]] — devices at each layer
