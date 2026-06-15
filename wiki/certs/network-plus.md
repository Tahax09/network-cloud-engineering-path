---
cert: CompTIA Network+
exam_code: N10-009
status: In Progress
target_date: TBD
lessons_completed: 3
---

# CompTIA Network+ N10-009

## Exam Overview
- **Format:** 90 questions, 90 minutes
- **Passing score:** 720/900
- **Question types:** Multiple choice, performance-based (drag-and-drop, simulations)
- **Domains:**

| Domain | Weight | Topic |
|--------|--------|-------|
| 1.0 | 23% | Networking Concepts |
| 2.0 | 20% | Network Implementation |
| 3.0 | 19% | Network Operations |
| 4.0 | 16% | Network Security |
| 5.0 | 22% | Network Troubleshooting |

## Progress by Objective

### Domain 1 — Networking Concepts (23%)

| Objective | Topic | Lesson | Status |
|-----------|-------|--------|--------|
| 1.0 | OSI Model | [[L01]] | ✅ Complete |
| 1.2 | Networking Appliances & Applications | [[L02]] | ✅ Complete |
| 1.0 | Network Virtualization & Cloud | [[L03]] | ✅ Complete |
| 1.x | IP Addressing & Subnetting | — | Pending |
| 1.x | Routing Protocols | — | Pending |
| 1.x | Switching & VLANs | — | Pending |
| 1.x | Wireless Networking | — | Pending |

### Domain 2 — Network Implementation (20%)

| Objective | Topic | Lesson | Status |
|-----------|-------|--------|--------|
| 2.0 | Routing Technologies | [[L09]] | ✅ Complete |
| 2.2 | Switching Technologies | [[L10]] | ✅ Complete |
| 2.3 | Wireless Devices and Technologies | [[L11]] | ✅ Complete |
| 2.4 | Physical Installations | [[L12]] | ✅ Complete |

### Domain 3 — Network Operations (19%)
*(not started)*

### Domain 4 — Network Security (16%)
*(not started)*

### Domain 5 — Network Troubleshooting (22%)
*(not started)*

## Key Exam Traps (collected from sessions)

| Trap | Correct Answer |
|------|---------------|
| "Connectionless protocol" | UDP (L4), not IP — even though IP is also connectionless |
| "Handles traffic spikes" cloud characteristic | Rapid Elasticity (not On-Demand) |
| Frame destination MAC when sending to remote host | Your default gateway's MAC, not the remote server |
| MPLS VPN = encrypted? | No — MPLS is logical separation, NOT encryption |
| "Community cloud" | Shared by a group/industry, not public or private |
| AWS Security Groups vs Network ACLs | SGs = stateful; ACLs = stateless (need both directions) |
| VPN encrypted by default? | IPsec VPN yes; MPLS VPN no |
| Frame destination when routing | Always the next-hop router's MAC, never the final destination's MAC |
| Longest prefix match vs AD | Longest prefix match is checked FIRST — AD only applies when same prefix from two sources |
| Static NAT vs PAT | Static = 1:1 fixed; PAT = many:1 using ports |
| HSRP vs VRRP | HSRP = Cisco proprietary; VRRP = open standard; both do active/standby |
| EIGRP feasible successor | Pre-computed backup — instant failover. Not all routes have one |
| BGP port | TCP 179; application layer; explicit neighbor config required |
| STP root bridge | Lowest Bridge ID (priority + MAC) wins; default priority = 32768 |
| PortFast + BPDU Guard | Always pair them — PortFast skips STP delay; BPDU Guard shuts port if switch connected |
| Native VLAN | Frames cross trunk untagged; mismatch = security risk + STP issues |
| EtherChannel requirements | Speed, duplex, mode, VLAN, native VLAN, STP must all match on member ports |
| RSTP alternate port | Pre-computed backup to root — instant failover, no re-election |
| Jumbo frames | > 1518 bytes; must be enabled end-to-end on every device in path |
| 2.4 GHz non-overlapping channels | Only 1, 6, 11 — any other channel selection causes interference |
| WPA2-PSK vs WPA2-Enterprise | PSK = shared passphrase (brute-forceable); Enterprise = 802.1X + EAP + RADIUS |
| WPA3 vs WPA2 cipher | WPA3 uses AES-GCMP; WPA2 uses AES-CCMP |
| WPA3 SAE | Prevents offline dictionary attacks — can't crack a captured handshake |
| Autonomous vs lightweight AP | Autonomous = standalone; lightweight = WLAN controller managed (CAPWAP) |
| MDF vs IDF | MDF = carrier/ISP handoff + aggregation; IDF = per-floor, patch panels + switches |
| UPS purpose | Graceful shutdown only — not for extended operation; generator handles that |
| Low humidity risk | Electrostatic discharge (ESD); high humidity risk = condensation/shorts |
| MDF fire suppression | Clean-agent (FM-200, Novec, CO₂) — never water-based near electrical equipment |

## Weak Areas to Revisit
*(populated as sessions progress)*

| Area | Flagged In | Priority |
|------|-----------|----------|
| — | — | — |

## Study Resources
- Coursera: CompTIA Network+ N10-009 (transcript-based study)
- Professor Messer (recommended for practice tests)
- raw/transcripts/ — all lesson transcripts

## Notes
- Connect every concept to the k3s lab — you're running L2–L7 networking live
- Use OSI-based troubleshooting in every scenario question (bottom-up or top-down)
- Performance-based questions will show network diagrams — know device placement cold
