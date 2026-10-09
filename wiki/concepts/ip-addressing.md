# IP Addressing

## IPv4 Classes
| Class | First Octet | CIDR | Use |
|-------|-------------|------|-----|
| A | 1–126 | /8 | Large orgs |
| B | 128–191 | /16 | Mid-size |
| C | 192–223 | /24 | Small |
| D | 224–239 | — | Multicast |
| E | 240–255 | — | Reserved |

127 = loopback (not Class A assignable)

## Special Address Ranges

| Range | Purpose |
|-------|---------|
| 10.0.0.0/8 | RFC 1918 private (Class A) |
| 172.16.0.0–172.31.255.255/12 | RFC 1918 private (Class B) |
| 192.168.0.0/16 | RFC 1918 private (Class C) |
| 169.254.0.0/16 | APIPA — DHCP failure indicator |
| 127.0.0.1 | Loopback — tests IP stack |
| 224–239 | Multicast (Class D) |

**Trap:** 172.15.x.x and 172.32.x.x are PUBLIC. Only 172.16–172.31 is private.

## CIDR / Subnet Reference
| CIDR | Mask | Host Bits | Usable Hosts |
|------|------|-----------|-------------|
| /24 | 255.255.255.0 | 8 | 254 |
| /25 | .128 | 7 | 126 |
| /26 | .192 | 6 | 62 |
| /27 | .224 | 5 | 30 |
| /28 | .240 | 4 | 14 |
| /29 | .248 | 3 | 6 |
| /30 | .252 | 2 | 2 |

**Formula:** 2ⁿ − 2 (n = host bits)
**Point-to-point:** /30 = 4 addresses, 2 usable

## Fourth-Octet Bit Values
128 | 64 | 32 | 16 | 8 | 4 | 2 | 1
- /25 → 128
- /26 → 192 (128+64)
- /27 → 224 (128+64+32)
- /28 → 240 (128+64+32+16)
- /30 → 252 (128+64+32+16+8+4)

## VLSM
Different mask lengths within the same address block. Supported by OSPF, EIGRP, BGP, RIPv2. RIPv1 = no VLSM.

## NAT Types
| Type | Rewrites | Direction |
|------|---------|-----------|
| SNAT | Source IP | Private → Public (outbound) |
| DNAT | Destination IP | Public → Private (inbound) |
| PAT/Masquerade | Source IP + Port | Many-to-one (home router) |

## Multicast Addresses
| Address | Protocol |
|---------|---------|
| 224.0.0.5 | OSPF all routers |
| 224.0.0.6 | OSPF DR/BDR |
| 224.0.0.9 | RIP |

## CLI Tools
```bash
ipcalc 192.168.1.0/26   # subnet calculator
ip addr show             # interface IPs + CIDRs
ip route show            # routing table
```

## Linked Lessons
- [[L07 - IP Addressing]] — full lesson
- [[L03 - Network Virtualization & Cloud Technologies]] — VPC and private address space
- [[GCP01 - Routes and Route Preferences]] — GCP subnet rules
