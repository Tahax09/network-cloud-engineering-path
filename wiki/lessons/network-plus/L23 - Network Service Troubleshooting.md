---
id: L23
aliases: [L23]
title: Network Service Troubleshooting
course: CompTIA Network+ N10-009
cert_track: network-plus
objective: "5.3 — Network Troubleshooting: Given a Scenario, Troubleshoot Common Issues with Network Services"
date: 2026-10-08
status: Complete
quiz_attempted: No
weak_areas: []
tags: [stp, rstp, root-bridge, port-roles, vlan-assignment, trunk-allowed-vlans, switch-acl, routing-table, default-route, static-route, dynamic-routing, dhcp-exhaustion, apipa, default-gateway, duplicate-ip, subnet-mask, on-net-off-net]
---

# L23 — Network Service Troubleshooting

## Summary
This moves up from Layer 1 ([[L22 - Troubleshooting Cabling and Physical Issues]]) to **Layers 2 and 3**. There are four groups: **switching issues** (STP root bridge and port states, wrong VLAN, trunk pruning, ACLs on switches), **route selection** (connected/static/dynamic routes, missing default route, unreachable next hop, routes not learned), **DHCP pool exhaustion**, and **incorrect IP configuration** (wrong gateway, duplicate IP, wrong subnet mask). Exam questions give a symptom ("can reach local hosts but not the internet", "169.254 address") and ask for the cause. This lesson revisits [[L09 - Routing Technologies]], [[L10 - Switching Technologies]], and [[L16 - IP Services]] from a troubleshooting angle.

---

## Core Concepts

### Switching Issues

#### Spanning Tree Protocol (STP) Faults

**STP's job:** with redundant links between switches, broadcasts and multicasts would loop forever (a **broadcast storm**: MAC table thrashing, CPU at 100%, network down). STP **blocks** redundant links and re-opens one if the active path fails ([[L10 - Switching Technologies]]).

**Root bridge election:** the switch with the lowest **Bridge ID** wins. Bridge ID = **priority** (default **32768**) + **MAC address**. When all priorities are left at the default, the **lowest MAC** wins, which is often the **oldest, slowest switch** in the closet.

**Problem:** A poorly placed root bridge (e.g., an access switch) forces traffic through a suboptimal path, because the spanning tree is built *outward from the root*.
**Fix:** Set the root explicitly on the core/distribution switch:
```
spanning-tree vlan 10 root primary          ! sets priority to 24576 (or lower if needed)
spanning-tree vlan 10 priority 4096         ! or set explicitly (multiples of 4096)
show spanning-tree vlan 10                  ! "This bridge is the root" + port roles/states
```

> **Transcript clarification:** The lecture says STP "will elect its own root bridge by default." What it means: **the election is automatic**, based on lowest Bridge ID, so without configuration you get whichever switch has the lowest MAC, not the one you want.

**STP vs RSTP terminology:**
| | 802.1D STP (original) | 802.1w RSTP |
|-|-------------|-------------|
| **Port states** | Disabled, **Blocking**, **Listening**, **Learning**, Forwarding | **Discarding** (replaces disabled/blocking/listening), **Learning**, Forwarding |
| **Port roles** | Root, Designated, (non-designated = blocked) | **Root**, **Designated**, **Alternate**, **Backup** (+ Disabled) |
| **Convergence** | 30–50 s (listening 15 s + learning 15 s, up to 20 s max-age) | ~1–2 s (proposal/agreement handshake) |

**Port roles:**
| Role | Definition | State |
|------|-----------|-------|
| **Root port** | Each non-root switch's port with the **lowest cost path to the root** | Forwarding |
| **Designated port** | The forwarding port for each segment. **All ports on the root bridge are designated** | Forwarding |
| **Alternate port** | Backup path to the root (via a different switch) | **Discarding** |
| **Backup port** | Backup to a designated port on the same segment (rare, hub-era) | Discarding |

**Path cost:** lower = better (e.g., 1 Gbps = 4, 10 Gbps = 2 in short-mode costs). The root port is chosen by **lowest cumulative cost to the root**.

**PortFast** skips listening/learning on edge ports (hosts), going straight to forwarding. Pair it with **BPDU guard** ([[L22 - Troubleshooting Cabling and Physical Issues]] err-disabled).

**STP symptoms on the exam:**
| Symptom | Cause |
|---------|-------|
| Network-wide outage, link lights solid, CPU spikes, MAC flapping logs | **Broadcast storm / switching loop** (STP disabled, or an unmanaged switch looping two ports) |
| Traffic between two core switches takes a long detour through an access switch | **Wrong root bridge** |
| Hosts take ~30 s to get a link/DHCP after boot | Legacy STP without **PortFast** on access ports |

#### Incorrect VLAN Assignment

A port is assigned to a VLAN by configuration, and can only talk to ports in that same VLAN ([[L10 - Switching Technologies]]).

| Device config | Symptom on the wrong VLAN |
|--------------|-------------------------|
| **DHCP** | Gets an IP from the **other VLAN's scope**, so it can reach the wrong resources (or none of the right ones) |
| **Static IP** | **Can't communicate with anything**: its gateway and peers are in a different subnet/broadcast domain |

```
show interfaces Gi1/0/12 switchport     ! "Access Mode VLAN: 20" — expected 10?
show vlan brief
```

**Trunk issues:** Trunks carry all VLANs by default. If someone restricts `switchport trunk allowed vlan`, or the VLAN doesn't exist on the far switch, that **VLAN is isolated** to one switch. Hosts on the same VLAN but on different switches can't talk.
```
show interfaces trunk                   ! "Vlans allowed on trunk" / "allowed and active"
switchport trunk allowed vlan add 30    ! 'add' — without it, you REPLACE the list (classic outage)
```
Also check a **native VLAN mismatch** between trunk ends (CDP logs `%CDP-4-NATIVE_VLAN_MISMATCH`).

#### ACLs on Switches

Switches support ACLs too (port ACLs, VLAN ACLs/VACLs) and can match on IP, port, MAC, or traffic type. An ACL with a misplaced deny (or one missing a needed permit before the **implicit deny**, [[L20 - Network Defense Techniques]]) can accidentally block:
- **DHCP** (UDP 67/68), so clients fall to APIPA
- **Multicast**, including **IPv6 Neighbor Discovery** (ICMPv6 RS/RA/NS/NA). Without RA, IPv6 hosts can't find their router or SLAAC an address ([[L16 - IP Services]])
- Routing protocol hellos (OSPF 224.0.0.5/6)

**Order matters (first match wins):**
```
permit host 10.0.0.1
deny   host 10.0.0.1      ! never reached — 10.0.0.1 already matched the permit
```

---

### Route Selection

#### Routing Table Sources ([[L09 - Routing Technologies]])
| Route type | How it gets there | Code (Cisco) |
|-----------|------------------|-------------|
| **Connected** | IP configured on an **up** interface. The router considers itself part of that network | `C` (+ `L` local /32) |
| **Static** | Admin-configured: destination + mask + next hop (or exit interface). Stays as long as the **exit interface toward the next hop is up** | `S` |
| **Dynamic** | Learned from OSPF/EIGRP/BGP/RIP neighbors | `O`, `D`, `B`, `R` |
| **Default route** | `0.0.0.0/0` (IPv6 `::/0`), the **gateway of last resort**. Used **only when nothing more specific matches** | `S*`, `O*E2`… |

Selection order recap: **longest prefix match** → lowest **administrative distance** → lowest **metric**.

```
ip route 0.0.0.0 0.0.0.0 203.0.113.1      ! static default route
show ip route                              ! "Gateway of last resort is 203.0.113.1"
```

> **Transcript clarification:** The lecture calls the default route "a special wildcard." It's the prefix **0.0.0.0/0**: a mask of all zeros matches every address. It isn't an ACL wildcard mask.

#### Common Routing Problems
| Problem | Symptom | Check |
|---------|---------|-------|
| **Missing default route** | Internal networks reachable, **internet unreachable** (router only knows connected + learned routes) | `show ip route` → "Gateway of last resort is not set" |
| **Next hop unreachable** | Static route stays configured but disappears from the table when its exit interface goes down; traffic falls to a less specific route or is dropped | `show ip route <dest>`, `ping <next-hop>` |
| **Routes not learned** | Remote subnets missing from the table | Is the **sender advertising** the network (`network` statements, redistribution)? Is the **receiver listening** (neighbor up, same area/AS, passive-interface not set, authentication matches)? |
| **Incorrect route / asymmetric routing** | One-way traffic, traceroute loops (`TTL exceeded` repeating between two hops) | `traceroute`, compare tables on both ends |

> **Transcript clarification:** The lecture says that when a next hop is unreachable "the dynamic route will fail." For **dynamic** routes, the neighbor adjacency drops and the protocol **withdraws** the route. For **static** routes, the route is removed only if the **exit interface goes down**. If the next hop dies but the local interface stays up (e.g., across a switch), the static route **stays in the table and black-holes traffic**. Fix: IP SLA tracking or a floating static route.

---

### DHCP Address Pool Exhaustion

The scope defines the address range plus options (default gateway, DNS) ([[L16 - IP Services]]). If **requests > available addresses**, the extra clients get no lease and self-assign **APIPA 169.254.x.x**: local link only, no gateway, no internet.

Example: a /24 scope with 250 usable addresses and 260 devices means the last 10 get APIPA.

**Exhaustion even with fewer active devices** happens because stale leases are still held by devices that left (guest Wi-Fi, conference rooms). Every visitor's phone holds a lease for the full lease duration.

**Fixes:**
| Fix | When |
|-----|------|
| **Shorten lease time** (e.g., 8 days → 1–4 hours on guest Wi-Fi) | High device turnover |
| **Expand the scope / resize the subnet** (/24 → /23) | Genuinely more devices than addresses |
| **Clear stale/abandoned leases** | Leases held by gone devices; also check for rogue clients (DHCP starvation attack, [[L19 - Network Attacks]]) |
| **Split into more VLANs/scopes** | Large flat networks |

> **Transcript clarification:** The lecture says DHCP servers "often end all the leases and start reissuing from the beginning." That isn't normal DHCP behavior. Servers **reclaim expired leases** and some reuse the oldest expired ones. Active leases are not revoked. The practical fixes are shorter leases or a bigger scope.

**Monitoring:** Alert on scope utilization (> 80%) as a preventive measure ([[L21 - Network Troubleshooting Methodology]] step 6).

---

### Incorrect IP Configuration

#### Wrong Default Gateway
The gateway **must be inside the host's subnet**. For `192.168.10.0/24`, the gateway must be `.1`–`.254`. A gateway outside the range can't be ARPed, so nothing leaves the subnet.
- Causes: manual typo, or the **DHCP scope's router option is wrong** (affects every client)
- Symptom: **local hosts reachable, remote/internet not**
- Check: `ip route` / `ip a` (Linux), `ipconfig /all` (Windows), `ifconfig` (legacy)

#### Wrong / Duplicate IP Address
- An IP in the wrong subnet: usually a **rogue DHCP server** ([[L19 - Network Attacks]]) or a static IP set on a host on the wrong VLAN
- **Duplicate IP:** a static device collides with a DHCP-assigned address (the static wasn't **excluded** from the scope, [[L16 - IP Services]]). OSes detect it through **gratuitous ARP** (IPv4) or **Duplicate Address Detection** (IPv6) and report "IP address conflict." Symptoms: intermittent connectivity for both hosts as the ARP caches flip
- Prevention: DHCP **exclusions/reservations** for static devices, DHCP snooping

#### Wrong Subnet Mask, and On-Net vs Off-Net

How a host decides where to send a packet:
```
Destination in MY subnet (per MY mask)?
   ├─ yes (on-net)  → ARP for the DESTINATION's MAC → send frame directly
   └─ no  (off-net) → ARP for the DEFAULT GATEWAY's MAC → send frame to router
```
If the host's mask disagrees with the real network, it makes the wrong decision:
- **Mask too large** (e.g., /16 instead of /24): the host thinks remote hosts are local and ARPs for them. Nobody answers, so remote communication fails (unless proxy ARP covers it up).
- **Mask too small** (e.g., /28 instead of /24): the host thinks local peers are remote and sends them via the gateway. It *may* work through the router, but inefficiently, and breaks if the router won't hairpin.

**The lecture's example (same IP, three masks):** take host `192.168.100.10`:
| Mask | Network | Broadcast | Hosts on-net |
|------|---------|-----------|--------------|
| /24 (255.255.255.0) | 192.168.100.0 | 192.168.100.255 | .1–.254 |
| /28 (255.255.255.240) | 192.168.100.0 | 192.168.100.15 | .1–.14 |
| /22 (255.255.252.0) | 192.168.100.0 | 192.168.103.255 | 100.1 – 103.254 |

**Key point:** the range is defined by the **mask**, not the third octet. `192.168.101.20` and `192.168.100.10` *are* on the same network under a /22.

> **Transcript note:** The lecture says there won't be specific subnetting questions on the test. N10-009 objective **1.7** does cover subnetting/CIDR ([[L07 - IP Addressing]]), so be able to compute ranges like the table above.

---

## Symptom → Cause Cheat Sheet

| Symptom | Most likely cause |
|---------|-------------------|
| Whole LAN down, switch CPUs pegged, MAC flapping | Switching loop / STP failure (broadcast storm) |
| Suboptimal L2 path through an access switch | Wrong STP root bridge |
| DHCP host gets an IP from another department's subnet | Wrong access VLAN on the port |
| Static-IP host can't reach anything after moving desks | Port in a different VLAN |
| Same-VLAN hosts on two switches can't talk | VLAN not allowed on the trunk / missing on far switch |
| Clients get 169.254.x.x | DHCP unreachable: **pool exhausted**, ACL blocking 67/68, wrong VLAN, relay missing |
| IPv6 hosts can't find a router | ACL blocking ICMPv6 / Router Advertisements |
| Internal OK, internet unreachable (from the router) | **Missing default route** |
| Internal OK, internet unreachable (one host) | **Wrong default gateway** on the host |
| Remote subnet missing from the routing table | Routing protocol neighbor/advertisement issue |
| Traceroute bounces between two routers | Routing loop |
| Intermittent connectivity, "IP conflict" warning | **Duplicate IP** |
| Can reach some local hosts but not others, or remote fails oddly | **Wrong subnet mask** |

---

## Key Terms

| Term | Definition |
|------|-----------|
| Broadcast storm | Frames looping endlessly in a switched loop |
| Root bridge | STP reference switch; lowest Bridge ID (priority + MAC) |
| Bridge priority | Default 32768; set in multiples of 4096 to control root election |
| Root port | Non-root switch's lowest-cost port toward the root |
| Designated port | Forwarding port for a segment; all root bridge ports |
| Alternate port | RSTP backup path to the root; discarding |
| Blocking / listening / learning / forwarding | 802.1D port states |
| Discarding | RSTP state replacing disabled/blocking/listening |
| Path cost | STP link cost; lower is better |
| PortFast | Edge port skips listening/learning |
| Trunk allowed VLANs | VLAN list a trunk carries; misconfiguration isolates VLANs |
| VACL / port ACL | ACLs applied on switches |
| Connected route | Network of an up, addressed interface |
| Static route | Manually configured destination + next hop |
| Dynamic route | Learned via a routing protocol |
| Default route / gateway of last resort | 0.0.0.0/0; used when no specific match |
| Longest prefix match | Most specific route wins |
| DHCP pool exhaustion | Scope has no free addresses; clients get APIPA |
| Lease time | Duration a client holds a DHCP address |
| APIPA | 169.254.0.0/16 self-assigned address when DHCP fails |
| Duplicate IP | Two hosts with the same address; detected via gratuitous ARP / DAD |
| On-net / off-net | Destination inside / outside the host's subnet per its mask |

---

## Real-World Connections

**k3s lab:**
- **IP exhaustion is real in Kubernetes:** each node gets a pod CIDR slice from `--cluster-cidr` (default `10.42.0.0/16`, a /24 per node = ~254 pods). On a big node, or if you set `--node-cidr-mask-size` too small, pods stick in `ContainerCreating` with `failed to allocate for range`, which is DHCP pool exhaustion for pods. **Service CIDR** (`10.43.0.0/16`) can exhaust the same way.
- **Overlapping CIDRs = the subnet mask problem:** if your home LAN is `10.42.x.x` or `10.43.x.x`, node routing breaks because the host thinks remote pod IPs are on-net (or vice versa). Check with `ip route` on a node: you should see `10.42.X.0/24 via <node-ip> dev flannel.1` for other nodes.
- Default route debugging in a pod: `kubectl exec <pod> -- ip route` shows the default via the CNI gateway (the `cni0` bridge IP).

**AWS:**
| Lesson issue | VPC equivalent |
|-------------|---------------|
| DHCP pool exhaustion | **Subnet IP exhaustion**: AWS reserves **5 IPs per subnet**; EKS VPC CNI assigns real VPC IPs to every pod, so /24 subnets run out fast (fix: prefix delegation, secondary CIDR) |
| Missing default route | Private subnet route table lacking `0.0.0.0/0 → nat-gw` → instances can't reach the internet |
| Wrong next hop | Route pointing to a deleted NAT GW / ENI shows as **blackhole** in the route table |
| Routes not learned | VGW **route propagation** disabled; BGP not advertising over VPN/Direct Connect |
| STP / VLANs | Don't exist in a VPC: no L2 broadcast domain to loop |

**Enterprise:** "VLAN missing from the trunk" is often caused by `switchport trunk allowed vlan 30` typed **without `add`**, which replaced the list and dropped every other VLAN. That's a famous outage-causing command, and a reason for change management ([[L13 - Organizational Processes and Procedures]]).

---

## Troubleshooting Scenario

**Scenario:** After a new conference-room expansion, Monday-morning users on the guest Wi-Fi report "connected, no internet." Some laptops work, others don't. Affected clients show `169.254.x.x`. The guest scope is `10.50.0.0/24` with an **8-day lease**. Corporate users are unaffected.

**Analysis ([[L21 - Network Troubleshooting Methodology]]):**
1. **Identify:** guest only, some clients, APIPA, started after more people began using the space (**what changed**: more devices).
2. **Theory:** DHCP pool exhaustion: 254 addresses, high turnover, 8-day leases keep last week's visitors' phones holding addresses.
3. **Test:** DHCP server scope stats show **100% utilization**, with most leases belonging to clients not seen in days. Confirmed.
4. **Plan:** shorten the guest lease to 2 hours, resize the guest subnet to `/23` (update the gateway mask on the SVI/router and the DHCP scope together; a mismatch would create a subnet-mask problem). Consider the effect: existing clients renew normally.
5. **Implement** in the change window (or the lease change immediately if low-risk).
6. **Verify:** clients get `10.50.x.x`, internet works, scope utilization ~40%. **Preventive:** SNMP/syslog alert at 80% scope use.
7. **Document.**

---

## Hands-On Ideas

1. **STP inspection (Packet Tracer/GNS3):** Build a triangle of 3 switches, run `show spanning-tree`, identify the root, root ports, and the alternate (blocked) port. Then set `spanning-tree vlan 1 priority 4096` on a different switch and watch the roles change.
2. **Trunk pruning outage:** `switchport trunk allowed vlan 20` (no `add`) on a trunk carrying 10, 20, 30, and observe VLAN 10/30 isolation. Fix with `add`.
3. **Routing table reading on Linux:**
   ```bash
   ip route                              # default via ... = gateway of last resort
   ip route get 8.8.8.8                  # which route/interface would be used
   sudo ip route del default             # (lab VM!) → internet fails, LAN works
   ```
4. **Subnet mask misconfig:** On a lab VM, set `/16` instead of `/24` (`ip addr add 192.168.1.50/16 dev eth1`) and ping an off-subnet host. Watch `ip neigh` fill with `FAILED` ARP entries for remote IPs.
5. **k3s pod CIDR:** `kubectl get nodes -o jsonpath='{.items[*].spec.podCIDR}'` shows each node's /24 "DHCP scope" for pods.

---

## Quiz

1. All switches have default STP priority. Which switch becomes root?
   - A) The one with the most ports  B) The newest switch  C) The one with the highest bandwidth uplink  D) The one with the lowest MAC address

2. In RSTP, which port role is in the discarding state as a backup path to the root?
   - A) Root port  B) Designated port  C) Alternate port  D) Edge port

3. All ports on the root bridge have which role?
   - A) Root  B) Alternate  C) Designated  D) Backup

4. A user moves desks. Their PC has a static IP and now can't reach anything, including the gateway. Others at the new desk are fine. What's most likely?
   - A) The new port is in a different VLAN  B) DHCP exhaustion  C) Missing default route  D) Duplicate IP

5. Hosts in VLAN 30 on Switch A can't reach VLAN 30 hosts on Switch B. Other VLANs work across the trunk. What should you check?
   - A) The STP root  B) The trunk's allowed VLAN list  C) The DHCP lease time  D) PoE budget

6. After a new ACL is applied to a switch, IPv6 clients can no longer discover their default router. What is the ACL most likely blocking?
   - A) TCP 443  B) UDP 53  C) ARP  D) ICMPv6 Router Advertisements (Neighbor Discovery)

7. A router can reach all internal subnets but nothing on the internet. `show ip route` says "Gateway of last resort is not set." What's the fix?
   - A) Add a default route  B) Enable PortFast  C) Expand the DHCP scope  D) Change the subnet mask

8. Which route does a router use for a packet to 10.1.1.5 if it has 10.0.0.0/8, 10.1.0.0/16, and 0.0.0.0/0?
   - A) 0.0.0.0/0  B) 10.0.0.0/8  C) 10.1.0.0/16  D) It load-balances across all three

9. OSPF neighbors are up, but a remote subnet is missing from Router B's table. What should you check first?
   - A) Whether Router A is advertising that network  B) Router B's DHCP scope  C) STP priority  D) Cable category

10. Twenty laptops in a busy training room get 169.254.x.x addresses, while earlier arrivals have valid IPs. What's the most likely cause?
    - A) Wrong default gateway  B) Duplicate IP  C) DHCP address pool exhaustion  D) Missing default route

11. A host is `192.168.10.50/24` with default gateway `192.168.20.1`. What happens?
    - A) Everything works  B) Nothing is reachable  C) Only DNS fails  D) Local hosts reachable; remote networks unreachable

12. A host at `10.0.5.20` has its mask set to /16 instead of /24. Communication with `10.0.9.10` (a remote subnet behind the router) fails. Why?
    - A) The host thinks 10.0.9.10 is on-net and ARPs for it directly instead of using the gateway  B) The host sends everything to the gateway  C) DHCP exhaustion  D) STP blocking

13. Two hosts intermittently lose connectivity, and Windows shows "IP address conflict." What's the cause, and the best prevention?
    - A) Wrong mask; use /24  B) Duplicate IP; exclude/reserve static addresses in DHCP  C) Missing route; add a default  D) Trunk pruning; add VLAN

14. Under a /22 mask, are `192.168.100.10` and `192.168.103.200` on the same network?
    - A) No, different third octets  B) Yes, 192.168.100.0–103.255 is one /22  C) Only if a router connects them  D) Only with VLAN tagging

**Answers:** 1-D, 2-C, 3-C, 4-A, 5-B, 6-D, 7-A, 8-C, 9-A, 10-C, 11-D, 12-A, 13-B, 14-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[L07 - IP Addressing]] — IP addressing, subnetting/CIDR (subnet mask problems)
- [[L09 - Routing Technologies]] — Routing table, static/dynamic routes, AD/metric, default route
- [[L10 - Switching Technologies]] — VLANs, trunks, STP/RSTP, PortFast, BPDU guard
- [[L16 - IP Services]] — DHCP scopes/leases/exclusions, APIPA, SLAAC/RA
- [[L19 - Network Attacks]] — Rogue DHCP, DHCP starvation
- [[L20 - Network Defense Techniques]] — ACL processing, implicit deny
- [[L21 - Network Troubleshooting Methodology]] — Troubleshooting methodology
- [[L22 - Troubleshooting Cabling and Physical Issues]] — Layer 1 issues (check before Layer 2/3)
- [[concepts/ip-addressing]] — Subnetting reference
- [[certs/network-plus]] — Domain 5.3 objective
