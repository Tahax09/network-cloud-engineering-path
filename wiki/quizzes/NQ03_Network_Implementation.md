---
id: NQ03
title: "Quiz: Network Implementation"
course: CompTIA Network+
cert_track: network-plus
covers: L09 + Switching, Wireless, Physical Installations (L10–L12)
date: 2026-06-14
status: Reviewed
score: --
tags: [quiz, network-plus, routing, switching, vlan, stp, wireless, wpa, fhrp, nat, bgp, ospf, physical]
---

# NQ03 — Quiz: Network Implementation

Module 2 graded quiz. Covers L09 (Routing Technologies) plus switching, wireless, and physical installation content not yet ingested (L10–L12).

---

## Questions

**Q1.** Which of the following is used to manage the link between the outside plant and inside plant wiring?
- A) Punchdown Block
- B) Main Distribution Frame (MDF)
- C) Intermediate Distribution Frame (IDF)
- D) Switch

---

**Q2.** A network engineer wants to divide traffic on a switched physical network between organizational groups like engineering and marketing. What should they do?
- A) Configure OSPF
- B) Set up a Firewall
- C) Use FHRP
- D) Set Up VLANs

---

**Q3.** A network technician is adding additional access points to an existing wireless network. The organization wants all APs to broadcast the same SSID. What type of network is being described?
- A) Channel Bonding
- B) Extended Service Set (ESS)
- C) Basic Service Set (BSS)
- D) MIMO

---

**Q4.** A router is set up to allow multiple internal devices to connect to the internet using the same IP. Which translation technology is in use?
- A) Tunneling
- B) Virtual IP
- C) NAT
- D) PAT

---

**Q5.** A warehouse had wireless APs placed on the edge of the building to maximize coverage. The office next door is seeing the warehouse signal, and employees say coverage is limited at the opposite side of the warehouse. What is the most likely cause?
- A) An omnidirectional antenna was used instead of a directional antenna
- B) 5 GHz frequency usage
- C) Wireless Signal Reflection
- D) Overlapping Channel Interference

---

**Q6.** An engineer has designed a switched network with redundant connections. What protocol should be enabled to prevent problems with broadcast and multicast traffic?
- A) IGMP
- B) STP
- C) OSPF
- D) FHRP

---

**Q7.** How is an IP address assigned to a switch?
- A) Interface IP
- B) Console port IP
- C) Switched Virtual Interface (SVI) IP
- D) Virtual IP

---

**Q8.** One of your offices is in an area prone to heavy rain and flooding. Your MDF is in the basement. What type of monitoring is most important for this location?
- A) Water sensor in the MDF
- B) Water sensor in the switch room
- C) Temperature sensor in the switch room
- D) Temperature sensor in the MDF

---

**Q9.** The power company notifies you that power will be off for 2 hours. Which solution would prevent downtime?
- A) Power Distribution Unit
- B) Backup generator
- C) Redundant Power Support
- D) Uninterruptible Power Supply (UPS)

---

**Q10.** Users are reporting intermittent wireless connectivity in parts of the building. As the wireless admin, what should you check? *(choose 2)*
- A) AP Placement
- B) Encryption used
- C) SSID Assignments
- D) Site Survey

---

**Q11.** What device can potentially support jumbo frames?
- A) Firewall
- B) Router
- C) Switch
- D) Hub

---

**Q12.** What IP address is provided as the default gateway when routers are used for FHRP?
- A) Loopback IP
- B) Virtual IP
- C) Switched Virtual Interface
- D) Lowest IP in the group

---

**Q13.** What is one of the most common reasons to use BGP?
- A) Preventing a loop within a LAN
- B) Improving reconvergence times
- C) Sharing routes with a Layer 3 switch
- D) Exchanging router updates with a different ISP

---

**Q14.** What is the administrative distance of EIGRP?
- A) 90
- B) 110
- C) 1
- D) 0

---

**Q15.** When receiving traffic on a port, how does a switch know what VLAN the traffic is for? *(choose 2)*
- A) The VLAN is configured on the switch port
- B) The IP address
- C) The port number
- D) The 802.1q tag

---

**Q16.** When working with the 2.4 GHz range, which channel selection causes the least overlap when adjacent?
- A) 1, 6, 11
- B) 1, 4, 10
- C) 3, 6, 9
- D) 1, 3, 6

---

**Q17.** Which of the following can be a problem for switched networks with redundant connections that are not using spanning tree?
- A) ARP spoofing
- B) Denial of Service
- C) Broadcast Storms
- D) Traffic Floods

---

**Q18.** Which encryption and security techniques are used for WPA3? *(choose 2)*
- A) AES-GCMP
- B) PSK
- C) SAE
- D) AES-CCMP

---

**Q19.** Which protocol allows you to configure redundant switch links to work as a single virtual link between switches?
- A) STP
- B) FHRP
- C) LACP
- D) OSPF

---

**Q20.** Which of the following routing commands would allow a router to communicate to the 192.168.40.0/24 network via the router at 192.168.10.5?
- A) `ip route 192.168.40.0 255.255.255.0 192.168.10.5`
- B) `neighbor 192.168.10.5 remote-as 192.168.40.0 mask 255.255.255.0`
- C) `ip route 192.168.10.5 192.168.40.0 255.255.255.0`
- D) `network 192.168.40.0 255.255.255.0 area 0`

---

**Q21.** Which technology provides a failover for routers serving a network as a default gateway?
- A) STP
- B) FHRP
- C) DHCP
- D) LACP

---

**Q22.** Which technology provides the best redundancy for Layer 3 connectivity?
- A) LACP
- B) DHCP
- C) FHRP
- D) STP

---

**Q23.** Which routing protocol uses an autonomous system number to identify an organization?
- A) IS-IS
- B) BGP
- C) OSPF
- D) EIGRP

---

**Q24.** A technician wants wireless security that uses AES and depends on certificates on authenticating devices. Which option should they select?
- A) WPA-PSK
- B) WPA2-Enterprise
- C) WPA2-PSK
- D) WPA-Enterprise

---

**Q25.** You need a link-state routing protocol that uses a shortest path first calculation to find the best route. Which would you choose?
- A) IS-IS
- B) BGP
- C) EIGRP
- D) OSPF

---

**Q26.** What technology is needed to route requests from 192.168.10.100 to a web server on the public internet?
- A) SaaS
- B) STP
- C) VPN
- D) NAT

---

## Answer Key

| Q | Answer | Key Reasoning |
|---|--------|--------------|
| 1 | **B — MDF** | MDF = central demarcation point where outside plant (telco/ISP) meets inside plant wiring |
| 2 | **D — Set Up VLANs** | VLANs logically segment a switched network by group/department without physical separation |
| 3 | **B — ESS** | Extended Service Set = multiple APs sharing the same SSID; BSS = single AP |
| 4 | **D — PAT** | Many devices → one public IP using port tracking = PAT (Port Address Translation) |
| 5 | **A — Omnidirectional antenna instead of directional** | Omni radiates in all directions → leaks outside building + doesn't reach far side; directional would focus inward |
| 6 | **B — STP** | Spanning Tree Protocol blocks redundant paths to prevent broadcast storms from Layer 2 loops |
| 7 | **C — Switched Virtual Interface (SVI) IP** | Switches don't have physical interface IPs; SVI (VLAN interface) is used for management IP |
| 8 | **A — Water sensor in the MDF** | MDF is in the basement = flood risk; water sensor there detects the specific threat |
| 9 | **D — UPS** | UPS provides instant battery backup with zero transition downtime; generator takes ~30–60s to start |
| 10 | **A + D — AP Placement + Site Survey** | Intermittent coverage in specific areas = placement/coverage issue; site survey maps signal strength |
| 11 | **C — Switch** | Switches support jumbo frame MTU configuration (up to 9000 bytes); hubs cannot |
| 12 | **B — Virtual IP** | FHRP creates a VIP shared between physical routers; hosts configure VIP as default gateway |
| 13 | **D — Exchanging router updates with a different ISP** | BGP = EGP; primary use is inter-organization and ISP route exchange using ASNs |
| 14 | **A — 90** | EIGRP AD = 90; OSPF = 110; Static = 1; Connected = 0 |
| 15 | **A + D — VLAN configured on port + 802.1q tag** | Access port: VLAN assigned to port config; Trunk port: 802.1q tag in frame identifies VLAN |
| 16 | **A — 1, 6, 11** | Only three non-overlapping channels in 2.4 GHz; all other combinations cause interference |
| 17 | **C — Broadcast Storms** | Layer 2 loops without STP → broadcast frames loop infinitely → broadcast storm → network failure |
| 18 | **A + C — AES-GCMP + SAE** | WPA3 uses AES-GCMP-256 (encryption) and SAE (auth, replaces PSK); WPA2 uses AES-CCMP + PSK |
| 19 | **C — LACP** | Link Aggregation Control Protocol (802.3ad) bonds multiple links into one logical link; STP blocks them |
| 20 | **A — `ip route 192.168.40.0 255.255.255.0 192.168.10.5`** | Cisco static route syntax: `ip route [destination] [mask] [next-hop]` — order matters |
| 21 | **B — FHRP** | FHRP = First Hop Redundancy Protocol; specifically designed for default gateway failover |
| 22 | **C — FHRP** | FHRP provides L3 gateway redundancy; LACP = L2 link aggregation; STP = L2 loop prevention |
| 23 | **B — BGP** | BGP requires an ASN (Autonomous System Number) to identify each organization |
| 24 | **B — WPA2-Enterprise** | Enterprise mode = 802.1X + RADIUS + certificates; PSK = pre-shared key, no certificates |
| 25 | **D — OSPF** | Link-state + Dijkstra SPF algorithm = OSPF; EIGRP is hybrid; BGP is path-vector |
| 26 | **D — NAT** | 192.168.10.100 is RFC 1918 private — needs NAT to reach the public internet |

---

## Exam Traps in This Quiz

| Trap | Detail |
|------|--------|
| MDF vs IDF | MDF = where outside plant meets inside; IDF = intermediate distribution within a building floor/wing |
| PAT vs NAT | "Multiple devices, same IP" = PAT specifically; NAT is the broader category |
| BSS vs ESS | Single AP = BSS; Multiple APs, same SSID, roaming = ESS |
| STP vs LACP | STP = blocks redundant links (prevents loops); LACP = bundles links into one (aggregation) |
| UPS vs Generator | UPS = zero-downtime instant switchover; Generator = long-duration but takes time to start |
| WPA2-PSK vs WPA2-Enterprise | PSK = shared password; Enterprise = 802.1X + RADIUS + certificates |
| WPA3: SAE not PSK | WPA3 replaces PSK with SAE; replaces AES-CCMP with AES-GCMP |
| EIGRP AD = 90, OSPF = 110 | EIGRP is more trusted (lower AD) than OSPF |
| ip route syntax | `ip route [destination] [mask] [next-hop]` — destination comes before next-hop |
| FHRP vs STP scope | FHRP = L3 gateway redundancy; STP = L2 loop prevention — completely different problems |

---

## Weak Areas to Revisit

*(fill in after submission)*

## Lesson Cross-Reference

| Questions | Content |
|-----------|---------|
| Q4, Q13, Q14, Q20, Q21, Q22, Q23, Q25, Q26 | [[L09]] — Routing Technologies (NAT/PAT, BGP, EIGRP AD, static routes, FHRP, OSPF) |
| Q2, Q6, Q7, Q11, Q15, Q17, Q19 | Switching Technologies (VLANs, STP, SVI, jumbo frames, LACP) — L10 (pending) |
| Q3, Q5, Q10, Q16, Q18, Q24 | Wireless Technologies (ESS, antennas, site survey, channels, WPA3) — L11 (pending) |
| Q1, Q8, Q9 | Physical Installations (MDF/IDF, environmental monitoring, UPS) — L12 (pending) |
