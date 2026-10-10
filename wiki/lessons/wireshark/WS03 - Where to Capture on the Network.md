---
id: WS03
aliases: [WS03]
title: Where to Capture on the Network
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Getting Started
date: 2026-10-10
status: Complete
quiz_attempted: No
weak_areas: []
tags: [wireshark, capture-point, endpoint-capture, span, port-mirroring, oversubscription, tap, aggregating-tap, cloud-capture, vtap, traffic-mirroring, packet-mirroring, offload]
---

# WS03 — Where to Capture on the Network

## Summary
**Where** you capture decides **what truth you see**. The lesson covers four capture points, from cheapest to most faithful: **(1) on the endpoint** (quick, free, but adds load and shows only the host's view), **(2) a SPAN / mirror port** on a switch (the real on-the-wire view, but easy to **oversubscribe**, so packets get dropped), **(3) a network TAP** (physical, passive, sees everything, costs money), and **(4) cloud/server environments** (use the provider's **virtual TAP / traffic mirroring** or a dedicated capture VM; installing Wireshark on a production server is a **last resort**, and then only from the CLI). The practical advice: put **TAPs on a few key links in advance** so your network is **"analyzable"** before the next incident.

---

## Core Concepts

### The reference topology
```
 Internet/Cloud
      │
  [Firewall]
      │        ◀── TAP here (internet edge)
  [Router]
      │        ◀── TAP here (core uplink)
  [Core switch] ── SPAN ──▶ Wireshark laptop
   ├── [Switch] ── servers   (cloud: virtual TAP / mirroring)
   └── [Switch] ── clients   (W = Wireshark on the endpoint)
```

### 1. On the endpoint
| ✅ Pros | ❌ Cons |
|--------|--------|
| Quick, free, nothing to configure on the network | **Adds load** to a machine that's already struggling |
| Shows exactly what that host sent and received | It's the **host's perception**, not what actually crossed the wire |
| A good first step to scope an application problem | Needs admin rights and software installed on the user's device |

**Gotcha (beyond the lecture): NIC offloading.** On the endpoint, the capture happens *before* the NIC segments and checksums outgoing packets. You'll see **"giant" TCP segments** (e.g., 20 KB, from TSO/GSO) and **"checksum incorrect [maybe caused by TCP checksum offload]"**. These are capture artifacts, not network problems. Check with `ethtool -k eth0` on Linux.

### 2. SPAN / port mirroring
The switch **copies** traffic from source ports (or VLANs) to a **destination (monitor) port** where the analyzer sits.
```
! Cisco IOS example
monitor session 1 source interface Gi1/0/1 both
monitor session 1 destination interface Gi1/0/24
show monitor session 1
```
- Supported by almost every managed switch (Cisco, HPE/**Aruba**, Juniper, …). Look up your vendor's syntax. Remote variants: **RSPAN** (over a VLAN) and **ERSPAN** (GRE-encapsulated over IP).
- **Advantage:** you see what actually traveled the network path, not just one host's view.

**The big risk: oversubscription.**
| Sources | Potential load | SPAN destination | Result |
|---------|---------------|------------------|--------|
| 3 × 1 Gbps ports, **full duplex, "both" directions** | 3 × (1 Tx + 1 Rx) = **6 Gbps** | 1 Gbps | Anything over 1 Gbps during a burst is **silently dropped** |

In Wireshark, dropped mirror traffic shows up as **`[TCP Previous segment not captured]`** and **`[TCP ACKed unseen segment]`**. Many of those on a SPAN capture point to an **overloaded SPAN**, not a real network problem.

**Other SPAN caveats (beyond the lecture):**
- The switch usually **won't mirror errored frames** (CRC/runts), so physical-layer problems are invisible.
- **Mirroring is lower priority** than forwarding, and busy switches shed it first.
- **Timestamps** shift slightly, and VLAN tags may be stripped depending on the platform.
- The switch's own load matters: it's already busy forwarding production traffic.

### 3. Network TAP (Test Access Point)
A hardware device inserted **inline** on a link that **passively copies** everything to a monitor port.
| ✅ Pros | ❌ Cons |
|--------|--------|
| Sees **every** frame on that link, including errored frames | **Costs money**: hundreds of dollars basic, thousands for enterprise / packet brokers |
| **No switch involved**, so no SPAN config and no oversubscription of a single link | Inserting one needs a brief link outage, so install it *before* you need it |
| Copper or fiber; passive fiber TAPs keep passing traffic even if TAP power fails | **Aggregating TAPs** (combining Tx+Rx onto one monitor port) *can* oversubscribe, the same as SPAN |

> **Lecture nuance:** "I can't over-provision it" is true for a **non-aggregating** TAP with **separate Tx/Rx monitor outputs**. An aggregating TAP merges both directions onto one port, so 1 Gbps full duplex (2 Gbps potential) going out a 1 Gbps monitor port can drop packets.

**The instructor's recommendation: make the network "analyzable."** Pre-install one or two TAPs on **key choke points**, e.g. internet edge (firewall ↔ router) and the core uplink, so you can walk up, connect Wireshark, and capture without reconfiguring anything. Performance problems and attacker traffic both tend to cross those links.

### 4. Servers and the cloud
| Option | Notes |
|--------|-------|
| **Provider virtual TAP / traffic mirroring** | Preferred. **AWS VPC Traffic Mirroring** (ENI → mirror target NLB/ENI, VXLAN-encapsulated), **GCP Packet Mirroring** (to an internal LB collector), **Azure virtual network TAP** (check regional availability) |
| **Dedicated capture VM** in the same environment | Receives mirrored traffic and runs `dumpcap` with a ring buffer ([[WS02 - Your First Packet Capture]]) |
| **Wireshark on the production server** | **Last resort.** The server is already busy. If you must, use **CLI capture** (tcpdump/dumpcap) with a tight **capture filter** and ring buffer, never the GUI |

---

## Capture-Point Decision Table

| Situation | Best capture point |
|-----------|-------------------|
| A single user's app is slow; you need answers in 5 minutes | **Endpoint** (accept the caveats) |
| Need the on-the-wire view between the server switch and the client | **SPAN** on the access/distribution switch (watch utilization) |
| Lots of `Previous segment not captured` on a SPAN capture | Suspect **SPAN oversubscription** → narrow the sources or move to a **TAP** |
| Suspected physical errors (CRC) | **TAP**: SPAN won't show bad frames |
| Security investigation at the internet edge | **Pre-installed TAP** at firewall ↔ router |
| Workloads in AWS/GCP/Azure | **Traffic/Packet Mirroring** to a capture VM |
| Kubernetes pods | Capture in the pod's network namespace (below), not on the app container |

---

## Key Terms

| Term | Definition |
|------|-----------|
| Capture point | The location in the network where packets are collected; it determines what you see |
| SPAN / port mirroring | Switch copies traffic from source ports/VLANs to a monitor port |
| RSPAN / ERSPAN | Remote SPAN over a VLAN / encapsulated in GRE over IP |
| Oversubscription (over-provisioning) | Mirrored sources exceed the monitor port's bandwidth, so packets are dropped |
| `TCP Previous segment not captured` | Wireshark expert flag for a gap in sequence numbers; on a SPAN it's often capture loss |
| TAP | Inline hardware device that passively copies all traffic on a link |
| Aggregating TAP | TAP that merges both directions onto one monitor port (can oversubscribe) |
| Packet broker / visibility fabric | Device that aggregates, filters, and distributes TAP/SPAN feeds to tools |
| Virtual TAP / traffic mirroring | Cloud provider feature that copies VM/ENI traffic to a collector |
| NIC offload (TSO/GSO/checksum) | NIC does segmentation and checksums; endpoint captures show giant or "bad checksum" packets |
| Analyzable network | A network with pre-placed capture points ready before an incident |

---

## Real-World Connections

**Network+ links:** This is objective 3.2/5.5 in practice: [[L14 - Network Monitoring]] (port mirroring) and [[L25 - Troubleshooting Tools]] (**TAP vs SPAN**: a TAP is passive, doesn't drop, and passes errored frames). Oversubscription is the same bottleneck idea as [[L24 - Troubleshooting Network Performance Issues]].

**k3s lab (Kubernetes capture points):**
| Capture point | k3s equivalent |
|--------------|----------------|
| Endpoint | `kubectl debug -it <pod> --image=nicolaka/netshoot --target=<container> -- tcpdump -i eth0 -w -`: an ephemeral container shares the pod's network namespace, so nothing is installed in the app image |
| SPAN on a switch | Capture on the node's **`cni0` bridge** or the pod's **veth** (`tcpdump -i vethXXXX`); this sees all pods on that node |
| TAP on the uplink | Capture the node's **`eth0`** for traffic between nodes (Flannel **VXLAN UDP 8472**, so decode as VXLAN to see inner packets) |
| Cloud vTAP | **Kubeshark** (eBPF/AF_PACKET agents on every node), effectively a cluster-wide mirror |

**Cloud:** AWS mirror sessions send traffic **VXLAN-encapsulated (UDP 4789)** to the target. In Wireshark, Decode As → VXLAN shows the original packets. VPC Flow Logs are **metadata only** and are not a substitute for packet capture.

**Security / SOC:** IDS sensors (Suricata/Zeek) sit on exactly these points: TAPs at the edge and SPANs inside. It's the same placement decision as for Wireshark.

---

## Troubleshooting Scenario

**Scenario:** A SPAN capture of a busy server switch shows hundreds of `[TCP Previous segment not captured]` and `[TCP ACKed unseen segment]` flags. The app team concludes "the network is dropping packets." The SPAN session mirrors 4 × 1 Gbps server ports (`both`) to one 1 Gbps monitor port.

**Analysis ([[L21 - Network Troubleshooting Methodology]]):**
1. **Question the obvious, including the measurement tool.** *ACKed unseen segment* means the receiver acknowledged data the capture never saw, so the data **did** arrive. The **capture** missed it.
2. The potential load is 4 × 2 Gbps = **8 Gbps** into a **1 Gbps** monitor port, which is classic SPAN oversubscription.
3. **Test:** check the monitor port's output drops (`show interfaces Gi1/0/24` → output drops climbing) and compare with the servers' own TCP retransmission counters (`netstat -s`). The servers show few retransmissions.

**Fix:** narrow the SPAN to the one server and direction in question (`source interface Gi1/0/3 rx`), use a 10 Gbps monitor port, or install a **TAP** on that server's link. Re-capture. If the gaps disappear, the "network drops" were capture artifacts.

---

## Hands-On Ideas
1. **Endpoint offload artifacts:** on a Linux node, run `sudo tcpdump -i eth0 -c 200 -w /tmp/off.pcap` during a large `scp`. Open it in Wireshark and look for frames much larger than 1514 bytes and checksum warnings. Then `sudo ethtool -K eth0 tso off gso off`, re-capture, and compare. (Turn offloads back on afterwards.)
2. **Simulate a SPAN view in k3s:** capture on `cni0` on a node while two pods talk (`curl` between them). You'll see both pods' traffic without touching either pod.
3. **Kubernetes "endpoint" capture without installing anything:** `kubectl debug` + netshoot into the Elasticsearch pod and capture port 9200.
4. **Plan your analyzable network:** sketch your home/lab topology and mark where you'd put a TAP (router ↔ switch uplink) versus where a SPAN would do (a managed switch, if you have one).
5. **Remote mirror (ERSPAN) in Wireshark:** if you ever receive ERSPAN or AWS mirror traffic, apply Decode As → *ERSPAN* / *VXLAN* to see the original frames.

---

## Quiz

1. What is the main downside of installing Wireshark directly on the complaining user's PC?
   - A) It adds load to an already struggling host and shows only that host's perception  B) It can't capture TCP  C) It requires a TAP  D) It only works on Wi-Fi

2. A SPAN mirrors three 1 Gbps full-duplex ports (both directions) to a 1 Gbps monitor port. What is the potential mirrored load?
   - A) 1 Gbps  B) 3 Gbps  C) 6 Gbps  D) 12 Gbps

3. On a SPAN capture, Wireshark shows many `TCP Previous segment not captured` and `ACKed unseen segment` flags, but the servers report almost no retransmissions. What's the most likely cause?
   - A) Duplex mismatch  B) SPAN oversubscription dropping mirrored packets  C) DNS failure  D) A routing loop

4. Which capture method sees every frame on a link, including errored frames, without involving the switch?
   - A) SPAN  B) Endpoint capture  C) Network TAP  D) NetFlow

5. When can a TAP oversubscribe?
   - A) Never  B) Only on fiber  C) Only when powered off  D) When it is an aggregating TAP combining Tx and Rx onto one monitor port

6. What is the instructor's recommended way to make a network "analyzable"?
   - A) Install Wireshark on every server  B) Mirror every switch port to one laptop  C) Pre-install TAPs at key choke points such as the internet edge and core uplinks  D) Rely on VPC Flow Logs

7. What's the preferred way to capture traffic from a VM in AWS or GCP?
   - A) Install the Wireshark GUI on the production VM  B) The provider's traffic/packet mirroring to a dedicated capture VM  C) Ask users to run speed tests  D) Use Flow Logs only

8. An endpoint capture shows 28,000-byte TCP segments and "checksum incorrect" warnings. What's most likely?
   - A) NIC offloading (TSO/checksum offload) artifacts on the capturing host  B) Jumbo frames on the WAN  C) Malware  D) SPAN oversubscription

**Answers:** 1-A, 2-C, 3-B, 4-C, 5-D, 6-C, 7-B, 8-A

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[WS02 - Your First Packet Capture]] — How to capture, ring buffers
- [[WS01 - Pre-Lesson - Your First Lab]] — Why the capture point mattered (client-side timing)
- [[wireshark]] — Wireshark reference
- [[wireshark-101]] — Course progress
- [[L14 - Network Monitoring]] · [[L25 - Troubleshooting Tools]] — SPAN vs TAP
- [[L24 - Troubleshooting Network Performance Issues]] — Bottlenecks / oversubscription
