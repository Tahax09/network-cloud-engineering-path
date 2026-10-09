---
id: L07
aliases: [L07]
title: IP Addressing
course: CompTIA Network+
cert_track: network-plus
objective: "1.7 — IP Addressing"
date: 2025-05-28
status: Complete
quiz_attempted: No
weak_areas: []
tags: [ip, ipv4, ipv6, subnetting, cidr, vlsm, rfc1918, apipa, nat, loopback]
---

# L07 — IP Addressing

## Summary
IPv4 classes, special address ranges, CIDR subnetting, and VLSM. Subnetting calculation is the most likely exam performance-based question. The special addresses (APIPA, loopback, RFC 1918) are testable cold.

## IPv4 Fundamentals
32-bit address, expressed as four decimal octets (0–255 each). Subnet mask defines the network/host split. Device ANDs its IP and destination IP with the mask to decide: same network → send directly; different network → send to default gateway.

## IPv4 Classes

| Class | First Octet | Default Mask | CIDR | Use |
|-------|-------------|--------------|------|-----|
| A | 1–126 | 255.0.0.0 | /8 | Large orgs |
| B | 128–191 | 255.255.0.0 | /16 | Mid-size orgs |
| C | 192–223 | 255.255.255.0 | /24 | Small networks |
| D | 224–239 | — | — | Multicast |
| E | 240–255 | — | — | Reserved |

**127 is missing from Class A** — reserved for loopback. Class A assignable = 1–126.

## Special Addresses — Memorize Cold

### RFC 1918 Private (not routable on public internet)

| Range | CIDR | Class |
|-------|------|-------|
| 10.0.0.0–10.255.255.255 | /8 | A |
| 172.16.0.0–172.31.255.255 | /12 | B |
| 192.168.0.0–192.168.255.255 | /16 | C |

**Exam trap:** The 172.x private range is ONLY 172.16–172.31. Addresses 172.15.x.x and 172.32.x.x are public.

### APIPA — 169.254.0.0/16
Assigned automatically when DHCP is unreachable. If you see 169.254.x.x → **DHCP server is not reachable**. Not routable.

### Loopback — 127.0.0.1
Sends packets back to the local IP stack without touching the network. Tests that TCP/IP stack is functional. If pinging 127.0.0.1 fails → IP stack is broken.

**Loopback interface** (different): Logical interface with a stable routable IP on a router/server. Never goes down like a physical interface. Used for management and BGP peering.

### Multicast — Class D (224–239)
Devices join groups and receive group traffic. OSPF = 224.0.0.5 (all routers) + 224.0.0.6 (DR/BDR). RIP = 224.0.0.9.

## CIDR Notation

| CIDR | Mask | Host Bits | Usable Hosts |
|------|------|-----------|-------------|
| /8 | 255.0.0.0 | 24 | ~16.7M |
| /16 | 255.255.0.0 | 16 | 65,534 |
| /24 | 255.255.255.0 | 8 | 254 |
| /25 | 255.255.255.128 | 7 | 126 |
| /26 | 255.255.255.192 | 6 | 62 |
| /27 | 255.255.255.224 | 5 | 30 |
| /28 | 255.255.255.240 | 4 | 14 |
| /29 | 255.255.255.248 | 3 | 6 |
| /30 | 255.255.255.252 | 2 | 2 |

**Formula:** Usable hosts = 2ⁿ − 2 (where n = host bits)

**Fourth-octet bit values (memorize):** 128, 64, 32, 16, 8, 4, 2, 1
- /25 → 128
- /26 → 128+64 = 192
- /27 → 128+64+32 = 224
- /28 → 128+64+32+16 = 240
- /30 → 128+64+32+16+8+4 = 252

**Network address** = all host bits 0. **Broadcast address** = all host bits 1. Neither is assignable.

**Point-to-point links use /30:** 4 total addresses, 2 usable — one per end, no waste.

## Subnetting Example
Split 192.168.1.0/24 into /26 subnets:

| Subnet | Network | Usable Range | Broadcast |
|--------|---------|-------------|-----------|
| 1 | 192.168.1.0/26 | .1–.62 | .63 |
| 2 | 192.168.1.64/26 | .65–.126 | .127 |
| 3 | 192.168.1.128/26 | .129–.190 | .191 |
| 4 | 192.168.1.192/26 | .193–.254 | .255 |

## VLSM — Variable Length Subnet Masking
Different-sized subnets within the same address block. Efficiently fits each segment to its actual host count.

Example: 172.16.10.0/24 with VLSM:
- /25 (126 hosts): 172.16.10.0/25 → for 100-host segment
- /26 (62 hosts): 172.16.10.128/26 → for 50-host segment
- /28 (14 hosts): 172.16.10.192/28 → for 12-host segment
- /28 (14 hosts): 172.16.10.208/28 → for another 12-host segment

Supported by: OSPF, EIGRP, BGP, RIPv2. **RIPv1 did NOT support VLSM.**

## NAT Types
- **SNAT:** Changes source IP (private → public for internet access)
- **DNAT:** Changes destination IP (public → private for inbound services)
- **PAT/Masquerade:** Many-to-one SNAT using port numbers — how home routers work

## Real-World Connections
- **AWS VPC:** Typically uses 10.0.0.0/16 for the VPC; subnets carved out as /24 per AZ
- **k3s pods:** Pod CIDR is 10.42.0.0/16 by default — a private RFC 1918 Class A range
- **Subnetting practice:** `ipcalc 192.168.1.0/26` on Linux gives you network, broadcast, and usable range instantly

```bash
# Linux subnetting tools
ipcalc 192.168.1.0/26
ip addr show         # see your VM's IP and CIDR
ip route show        # see what subnets are routed where
```

## Quiz Questions
1. A device shows IP 169.254.15.3. What is wrong?
2. What class is 172.20.5.14?
3. How many usable hosts in a /27 subnet?
4. You need a subnet for exactly 25 hosts. What prefix length? (Show your work.)
5. Split 10.0.0.0/24 into /27 subnets. What is the network address of the third subnet?
6. Is 172.33.0.1 a private or public address?

## Exam Traps
- 172.16–172.31 is private; 172.15 and 172.32 are public
- 169.254.x.x = APIPA = DHCP failure (but in GCP metadata server context it's deliberate — different meaning)
- Class A starts at 1, not 0. 127 is not a Class A — it's loopback.
- /30 = 2 usable hosts. Point-to-point standard.

## Related
- [[concepts/ip-addressing]] — full reference with subnetting tables
- [[L03 - Network Virtualization & Cloud Technologies]] — VPC subnets and private address space
- [[GCP01 - Routes and Route Preferences]] — GCP VPC subnet expansion rules
