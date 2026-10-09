---
id: L09
aliases: [L09]
title: Routing Technologies
course: CompTIA Network+
cert_track: network-plus
objective: "2.0 — Network Implementation: Routing Technologies"
date: 2026-06-14
status: Complete
quiz_attempted: No
weak_areas: []
tags: [routing, static-routing, dynamic-routing, ospf, eigrp, bgp, nat, pat, fhrp, hsrp, vrrp, subinterfaces]
---

# L09 — Routing Technologies

## Summary
Routers forward traffic between Layer 3 networks using destination IP addresses. Route selection follows a strict priority: longest prefix match → admin distance → metric → load balance. Dynamic protocols (OSPF, EIGRP, BGP) automate route learning and failover. NAT/PAT bridge private and public address spaces. FHRP protocols (HSRP, VRRP, GLBP) provide default gateway redundancy using a shared virtual IP.

---

## Core Concepts

### 1. How Routing Works

**Local vs Remote decision — happens at the sending host, not the router:**
- Destination on same subnet → ARP for the destination's MAC directly
- Destination on different subnet → ARP for the **default gateway's MAC**, wrap packet in frame to gateway

**Critical distinction — IP vs MAC across hops:**
```
PC1 → Router A → Router B → PC2

Frame hops:    [PC1 MAC → RouterA MAC] → [RouterA MAC → RouterB MAC] → [RouterB MAC → PC2 MAC]
Packet:        [PC1 IP ──────────────────────────────────────────────────────────── PC2 IP]
```
IP addresses stay end-to-end. MAC addresses are rewritten at every hop. The router consumes the incoming frame, reads the destination IP, and builds a new frame to the next hop.

---

### 2. Route Selection — Priority Order

When multiple routes match a destination, the router picks in this order:

| Priority | Criterion | Description |
|----------|-----------|-------------|
| 1st | **Longest prefix match** | Most specific route wins (/28 beats /24 beats /0) |
| 2nd | **Admin distance (AD)** | Lower AD = more trusted source. Used when same prefix learned via two different protocols |
| 3rd | **Metric** | Protocol-specific cost (hop count, bandwidth, delay). Lower = better |
| 4th | **Load balance** | Equal-cost paths → traffic split across them |

**Admin distance reference (common values):**

| Source | Admin Distance |
|--------|---------------|
| Connected interface | 0 |
| Static route | 1 |
| EIGRP | 90 |
| OSPF | 110 |
| RIP | 120 |
| BGP (eBGP) | 20 |

**Longest prefix match example:**
Destination: `172.16.1.15`

| Routing Table Entry | Range | Match? |
|--------------------|-------|--------|
| 172.16.1.0/24 | .1.0 – .1.255 | ✅ |
| 172.16.0.0/22 | .0.0 – .3.255 | ✅ |
| 172.16.0.0/16 | .0.0 – .255.255 | ✅ |
| 0.0.0.0/0 | everything | ✅ |

Router uses `/24` — the most specific match. **Specificity always beats priority number.**

---

### 3. Static Routing

Manually configured routes. Syntax:
```bash
# Cisco
ip route 172.16.2.0 255.255.255.0 10.0.0.2    # destination network, mask, next-hop IP

# Linux/Windows
route add 172.16.2.0/24 via 10.0.0.2
```

| Characteristic | Detail |
|----------------|--------|
| Converges fast | Active immediately when interface is up |
| No failover | Won't reroute if link fails — no topology awareness |
| Best for | Small networks, stub routes, default routes |
| 0.0.0.0/0 | Default route — "gateway of last resort" — matches everything |

---

### 4. Subinterfaces (Router-on-a-Stick)

One physical interface → multiple logical sub-interfaces, each serving a different VLAN.

```bash
# Cisco: sub-interface config for inter-VLAN routing
interface G0/0.10
  encapsulation dot1Q 10
  ip address 192.168.10.1 255.255.255.0

interface G0/0.20
  encapsulation dot1Q 20
  ip address 192.168.20.1 255.255.255.0
```

- Physical port must connect to a switch **trunk port** (carries all VLANs tagged)
- Router assigns each VLAN its own gateway IP via sub-interface
- Common on edge routers and firewall interfaces

---

### 5. Dynamic Routing Protocols

**IGP vs EGP:**

| Type | Scope | Protocols |
|------|-------|-----------|
| IGP (Interior Gateway Protocol) | Within one organization (autonomous system) | OSPF, EIGRP, RIP |
| EGP (Exterior Gateway Protocol) | Between organizations / ASes | BGP |

#### BGP (Border Gateway Protocol)
- The internet's routing protocol — runs between ISPs and large enterprises
- Requires an **Autonomous System Number (ASN)** — public (IANA-assigned) or private (64512–65535)
- **Explicit neighbor configuration** — no auto-discovery; you specify the neighbor's IP and ASN
- Application layer protocol using **TCP port 179**
- Path-vector protocol: makes decisions on AS path, not just metric

```bash
# BGP config example
router bgp 101
  neighbor 203.0.113.1 remote-as 102    # explicitly define peer
```

#### EIGRP (Enhanced Interior Gateway Routing Protocol)
- Originally Cisco-proprietary, now open standard
- **Hybrid metric:** bandwidth + delay + reliability + load
- Key concepts:
  - **Successor** — best route to a destination
  - **Feasible successor** — pre-computed backup route (instant failover if it exists)
  - **Topology table** — stores all routes, not just best ones
  - **Reported distance (RD)** — what your neighbor says its cost is
  - **Feasible distance (FD)** — your total cost including the link to your neighbor

#### OSPF (Open Shortest Path First)
- Open standard, most widely used IGP
- **Link-state protocol** — each router knows the full topology
- Uses **Dijkstra's Shortest Path First (SPF)** algorithm
- **Areas** — hierarchical design to limit LSA (Link State Advertisement) flooding
  - Area 0 = backbone; all other areas connect to Area 0
  - Updates stay within area unless topology changes
- **Router ID** — unique identifier for each OSPF router (usually highest loopback IP)
- **Neighbor relationship** — must form before exchanging routes (via Hello packets)
- Metric = **cost** (based on bandwidth; lower = better)

| Protocol | Type | Metric | Notes |
|----------|------|--------|-------|
| RIP | Distance vector | Hop count (max 15) | Legacy; slow convergence |
| EIGRP | Hybrid | BW + delay + reliability + load | Fast failover via feasible successor |
| OSPF | Link state | Cost (bandwidth) | Open standard, hierarchical areas |
| BGP | Path vector | AS path + policy | Internet routing, EGP |

---

### 6. NAT / PAT

**Purpose:** Translate private (RFC 1918) addresses to public addresses for internet routing.

| Type | Mapping | Use Case |
|------|---------|---------|
| **Static NAT** | 1 private IP ↔ 1 public IP (fixed) | Hosting a public-facing server |
| **Dynamic NAT** | Pool of public IPs; first-come first-served | When you have multiple public IPs |
| **PAT (Port Address Translation)** | Many private IPs → 1 public IP using port tracking | Home routers, most enterprise NAT |
| **Carrier-grade NAT** | ISP-level NAT before you even get a public IP | Mobile carriers, shared ISP addresses |

**PAT NAT table example:**

| Inside Local | Inside Global | Outside Global |
|-------------|--------------|---------------|
| 192.168.1.10:1024 | 203.0.113.5:40001 | 8.8.8.8:53 |
| 192.168.1.11:1025 | 203.0.113.5:40002 | 8.8.8.8:53 |

**Terminology:**
- **Inside local** — private IP of the internal host
- **Inside global** — public IP as seen from the internet (the NAT address)
- **Outside local/global** — how the internal device refers to the external server (usually same)

---

### 7. First Hop Redundancy Protocols (FHRP)

**Problem:** Hosts have a statically configured default gateway. If that router fails, traffic stops — even if a backup router exists.

**Solution:** FHRP creates a **virtual router** with a **virtual IP (VIP)** that multiple physical routers share. Hosts point to the VIP. Physical routers negotiate who responds.

| Protocol | Standard | Routers | Notes |
|----------|----------|---------|-------|
| **HSRP** | Cisco proprietary | 1 active + 1 standby | Most common in Cisco shops |
| **VRRP** | Open standard (RFC 5798) | 1 master + 1+ backup | Multi-vendor |
| **GLBP** | Cisco proprietary | Active + load-balanced | Also distributes load across routers |

**How it works:**
```
Hosts configured: default gateway = 192.168.1.1  (the VIP — not assigned to either physical router)

Router A: real IP 192.168.1.2 → currently ACTIVE (responds to 192.168.1.1)
Router B: real IP 192.168.1.3 → STANDBY (listening; takes over if A fails)

Switch just sees the VIP move from Router A's port to Router B's port on failover.
```

---

## Key Terms

| Term | Definition |
|------|-----------|
| Default gateway | Router IP a host sends remote traffic to; frame uses router's MAC |
| Longest prefix match | Most specific (longest subnet mask) route wins |
| Admin distance | Trust metric for route source; lower = more trusted |
| Metric | Protocol-specific cost to reach a destination |
| Static route | Manually configured route; no failover |
| Subinterface | Logical interface on a physical port, typically for VLAN routing |
| IGP | Interior Gateway Protocol — used within an AS (OSPF, EIGRP) |
| EGP | Exterior Gateway Protocol — used between ASes (BGP) |
| ASN | Autonomous System Number — identifies an organization in BGP |
| OSPF area | Logical subdivision of OSPF network to limit LSA flooding |
| Successor (EIGRP) | Best route to a destination |
| Feasible successor | Pre-computed backup route in EIGRP |
| LSA | Link State Advertisement — OSPF topology update message |
| Static NAT | 1:1 fixed mapping between private and public IP |
| PAT | Port Address Translation — many:1 using port numbers |
| Inside local | Private IP of internal host |
| Inside global | Public IP that external hosts see (NAT address) |
| FHRP | First Hop Redundancy Protocol — virtual gateway for HA |
| HSRP | Cisco FHRP — active/standby router roles |
| VRRP | Open-standard FHRP — master/backup router roles |
| GLBP | Cisco FHRP with load balancing across routers |
| VIP | Virtual IP — shared address used by FHRP cluster |

---

## Real-World Connections

**k3s lab:** Your k3s nodes use static routes and Linux kernel routing. Run `ip route show` — you'll see the cluster CIDR routes managed by Flannel/CNI injected into the kernel routing table. The pod-to-pod traffic is essentially dynamic routing within the cluster (BGP if using Calico).

**AWS VPC:** Route tables in VPC = static routes. AWS adds routes automatically for subnets (like connected routes). Internet Gateway route = `0.0.0.0/0 → igw-xxxxx`. VPC peering = static routes added to both sides. Transit Gateway = dynamic routing with BGP between attachments.

**GCP:** Cloud Router uses BGP (similar to EIGRP/OSPF but at cloud scale) for dynamic route exchange with Interconnect and VPN. Matches the GCP01 content on route types.

**Enterprise:** OSPF within the campus, BGP to the ISP — exactly what the transcript describes. HSRP/VRRP on your distribution layer switches to provide redundant default gateways for VLANs.

---

## Troubleshooting Scenario

**Scenario:** A host can ping its default gateway but cannot reach `8.8.8.8`. The network has two routers in the path and NAT at the edge.

**Debug approach:**
1. `ping 192.168.1.1` — gateway reachable? If yes, L2/L3 to gateway is fine
2. `traceroute 8.8.8.8` — where does it stop? Identifies the failing hop
3. On the edge router: `show ip route 8.8.8.8` — is there a default route (`0.0.0.0/0`)?
4. Check NAT translation table: `show ip nat translations` — are sessions being created?
5. If no NAT translations: ACL or NAT config missing for the source subnet
6. If route missing: no default route → add `ip route 0.0.0.0 0.0.0.0 [ISP-next-hop]`

---

## Hands-On Ideas

- `ip route show` on your k3s Ubuntu nodes — identify connected routes, pod CIDR routes, default route
- Configure static routes between two Linux VMs (`ip route add`) and test with ping
- Capture OSPF Hello packets with Kubeshark/tcpdump: `proto ospf` — see neighbor negotiation
- Run `ip nat translations` equivalent (`conntrack -L`) on a Linux NAT gateway
- Set up BIRD or FRRouting on Ubuntu to run OSPF between two VMs

---

## Quiz

**Q1.** A packet is sent from 192.168.1.10 to 10.0.0.5. The host's routing table has a default gateway of 192.168.1.1. What MAC address is used as the destination in the frame leaving the host?
- A) MAC of 10.0.0.5
- B) MAC of 192.168.1.1
- C) MAC of 192.168.1.10
- D) Broadcast FF:FF:FF:FF:FF:FF

**Q2.** A routing table has three entries matching 10.1.1.50: /24, /26, and /16. Which entry is used?
- A) /16 — largest range
- B) /24 — learned via OSPF
- C) /26 — longest prefix match
- D) /16 — lowest admin distance

**Q3.** Which routing protocol uses TCP port 179 and requires explicit neighbor configuration?
- A) OSPF
- B) EIGRP
- C) RIP
- D) BGP

**Q4.** What is the purpose of an EIGRP feasible successor?
- A) It's the primary route to the destination
- B) It's a pre-computed backup route for instant failover
- C) It's the OSPF neighbor relationship table
- D) It's the BGP AS path attribute

**Q5.** A business has 200 internal hosts sharing a single public IP. Which NAT type is in use?
- A) Static NAT
- B) Dynamic NAT
- C) PAT (Port Address Translation)
- D) Carrier NAT

**Q6.** VRRP differs from HSRP in that VRRP is:
- A) Cisco proprietary
- B) An open standard (RFC 5798)
- C) Load-balancing across all routers
- D) Only for IPv6

**Answers:** Q1-B, Q2-C, Q3-D, Q4-B, Q5-C, Q6-B

---

## Weak Areas to Revisit
*(none flagged yet)*
