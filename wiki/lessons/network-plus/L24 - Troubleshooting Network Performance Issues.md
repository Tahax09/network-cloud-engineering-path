---
id: L24
aliases: [L24]
title: Troubleshooting Network Performance Issues
course: CompTIA Network+ N10-009
cert_track: network-plus
objective: "5.4 — Network Troubleshooting: Given a Scenario, Troubleshoot Common Performance Issues"
date: 2026-10-08
status: Complete
quiz_attempted: No
weak_areas: []
tags: [congestion, contention, csma-ca, bottleneck, bandwidth, throughput, qos, latency, jitter, packet-loss, voip, wireless-interference, rf, channel-overlap, signal-degradation, attenuation, coverage, roaming, reassociation, disassociation, 802.11r, heat-map]
---

# L24 — Troubleshooting Network Performance Issues

## Summary
[[L22 - Troubleshooting Cabling and Physical Issues]] and [[L23 - Network Service Troubleshooting]] covered things that are **broken**. This lesson covers things that **work but slowly or badly**. There are three groups: **traffic volume** (congestion/contention, bottlenecks, bandwidth vs throughput), **quality of service metrics** (latency, jitter, packet loss, and why real-time traffic is affected first), and **wireless performance** (interference, signal degradation, insufficient coverage, roaming and disassociation). The exam pattern is "voice calls are choppy", "Wi-Fi slow in the conference room", or "internet slow at 9 AM", followed by the cause or fix.

---

## Core Concepts

### Traffic Volume

#### Congestion / Contention

**Shared media** means multiple devices compete for one transmission path. Only one can talk at a time.

| Medium | Access method | Contention? |
|--------|--------------|-------------|
| Hub / half-duplex Ethernet | **CSMA/CD** (collision detection) | Yes: collisions, backoff |
| **Switched full-duplex Ethernet** | Dedicated send + receive per port | **No**: the switch backplane is faster than any port |
| **Wi-Fi** (half-duplex by nature) | **CSMA/CA** (collision **avoidance**) | **Yes**: all clients on a channel share the airtime |

**Why Wi-Fi slows with more clients:** each client must listen, wait a random backoff, then send. More clients means more waiting, so **per-client throughput drops**, and it often shows up suddenly once a threshold is crossed. Collisions (hidden nodes) add retransmissions on top.

**Fixes:**
- Wired: make sure every port is **full duplex** (a duplex mismatch puts you back in half-duplex land, [[L22 - Troubleshooting Cabling and Physical Issues]]).
- Wireless:
  - **Disable low legacy data rates** (1, 2, 5.5 Mbps / 802.11b). Slow clients take far longer to send the same frame and **consume disproportionate airtime**. Removing those rates also shrinks each AP's effective cell, which encourages roaming.
  - Add APs (smaller cells, fewer clients each) and use **5/6 GHz** (more non-overlapping channels).

> **Transcript clarification:** The lecture says "decrease the wireless range or eliminate access to the slower speed systems." These are the **same fix**: raising the **minimum basic data rate** both keeps legacy slow clients off and shrinks the usable cell size.

**Airtime example:** a 1,500-byte frame at 1 Mbps occupies the air for ~12 ms. At 600 Mbps it takes ~0.02 ms. One legacy client can consume the airtime of hundreds of modern ones.

#### Bottlenecking

A **bottleneck** is the narrowest link that many devices must share. The fastest path is limited by its slowest segment.

```
Clients ── 1 Gbps ── Access switch ── 10 Gbps ── Core ── 200 Mbps ── Internet / WAN
                                                          ▲ bottleneck
```
- LAN: 1 Gbps+ wired, Wi-Fi 5/6/7 near or above 1 Gbps
- **WAN/internet: tens to hundreds of Mbps**, shared by everyone in the office

Know your **traffic flows** (NetFlow, [[L14 - Network Monitoring]]) and size the WAN for peak demand. Common oversubscription points: the internet uplink, access → distribution uplinks, a VPN concentrator, and a firewall's inspection throughput (often far below its interface speed when IPS/TLS inspection is on).

#### Bandwidth vs Throughput

| | Bandwidth | Throughput |
|-|-----------|-----------|
| Definition | **Maximum capacity** of a link (its rated speed) | **Actual delivered data rate** |
| Set by | Technology (1G interface) **or contract** | Bandwidth minus overhead, congestion, errors, retransmissions, latency |
| Example | 1 Gbps port to Metro Ethernet, but the contract limits it to **200 Mbps** (policed/shaped by the provider) | `iperf3` shows 185 Mbps |

**Exam trap:** the interface says `1000Mb/s`, but users only get ~200 Mbps → check the **contracted** rate (CIR), not the port speed.

---

### Quality of Service: Latency, Jitter, Packet Loss

Raw bandwidth isn't the whole story. With **first-come, first-served** queuing, a big file transfer can delay a small voice packet. **Real-time traffic** (VoIP, live video, interactive authentication) suffers first. Recorded video just buffers.

| Metric | Definition | Real-time target (VoIP rule of thumb) | Causes |
|--------|-----------|--------------------------------------|--------|
| **Latency (delay)** | Time for a packet to arrive | **≤ 150 ms one-way** (ITU-T G.114) | Distance, queuing/congestion, slow links, satellite (~600 ms RTT GEO) |
| **Jitter** | **Variation** in delay between packets | **< 30 ms** | Congestion with fluctuating queues, path changes, Wi-Fi contention |
| **Packet loss** | Packets never arrive (dropped) | **< 1%** | Full queues (tail drop), errors (CRC), collisions, policing |

**How each one sounds on a call:**
- **Latency:** people talk over each other, awkward pauses, echo
- **Jitter:** choppy, robotic audio. The **jitter buffer** smooths it, but adds latency, and overflow means drops
- **Loss:** gaps and clipped words

**Why VoIP drops rather than retransmits:** VoIP runs over **RTP over UDP**. A retransmitted voice packet arrives too late to be useful, so it's better to skip it. TCP apps (file transfer, web) retransmit, so loss shows up as **slowness**, not gaps.

**Voice packetization:** codecs send a packet every **20 ms** (50 pps per call direction). Jitter is any deviation from that steady cadence on arrival.

**Fixing QoS problems:**
1. **Classify and mark** traffic: DSCP **EF (46)** for voice, AF41 for video, etc.
2. **Queue / prioritize:** low-latency queue (LLQ / strict priority) for voice
3. **Shape/police** bulk traffic at the WAN edge
4. Add bandwidth if the link is truly saturated (QoS manages congestion; it doesn't create capacity)
5. Fix underlying errors (CRC, duplex, [[L22 - Troubleshooting Cabling and Physical Issues]]) that cause loss

---

### Wireless Performance Issues

Wi-Fi fundamentals are in [[L11 - Wireless Devices and Technologies]]. This section covers troubleshooting them.

#### Interference

**Other transmitters in the same frequency band raise the noise floor and cause retransmissions.**

| Source | Band |
|--------|------|
| **Microwave ovens** | 2.4 GHz (classic exam answer) |
| Bluetooth, baby monitors, cordless phones, wireless video senders | 2.4 GHz (some 5.8 GHz) |
| **Neighboring Wi-Fi networks / your own APs on overlapping channels** | Both (co-channel and adjacent-channel interference) |
| EMI from motors, fluorescent lighting, power equipment | Broadband noise |

**Fix:** choose channels properly. In 2.4 GHz use only **1, 6, 11** (non-overlapping). Prefer 5/6 GHz. Use a **Wi-Fi analyzer** / **spectrum analyzer** to see noise, other networks, and their channels. Controller-based systems **auto-adjust channel and power** (RRM), but still monitor them.

**Key metric: SNR** (signal-to-noise ratio) = signal (dBm) − noise floor (dBm). For example, −65 dBm signal and −92 dBm noise gives **27 dB SNR**. You want **≥ 25 dB** for voice/video-grade Wi-Fi. Interference raises the noise floor, so SNR drops even when the signal is unchanged.

#### Signal Degradation / Loss

- RF **attenuates quickly** with distance (free-space path loss).
- **Absorption by obstacles:** human bodies, **water** (aquariums, people, plants), concrete/brick walls, metal (elevators, filing cabinets, foil-backed insulation, low-E glass).
- **Frequency trade-off:**

| Band | Range / penetration | Speed / channels |
|------|-------------------|------------------|
| **2.4 GHz** | **Best**: goes through walls | Slowest, only 3 non-overlapping channels, crowded |
| 5 GHz | Medium | Faster, many channels |
| 6 GHz (Wi-Fi 6E/7) | Shortest | Fastest, cleanest spectrum |

That's why 2.4 GHz is still kept around: coverage for distant or obstructed clients and IoT.

**Signal strength reference (RSSI):**
| RSSI | Quality |
|------|---------|
| −30 to −50 dBm | Excellent |
| **−67 dBm** | **Minimum for VoIP/video** (common design target) |
| −70 dBm | OK for browsing/email |
| −80 dBm and below | Unreliable, frequent retries |

#### Insufficient Coverage

Too few APs for the area means **dead zones / weak signal**. Too many clients per AP means **contention** (see above).
- Plan with a **site survey** and **heat maps** (predictive, then validated on-site).
- Design for **capacity** (clients per AP, airtime), not just coverage.
- Tune **TX power** and channels. More power isn't always better: it causes co-channel interference and **asymmetric links**, where the client hears the AP but the AP can't hear the weaker client.

#### Client Disassociation and Roaming Issues

**Roaming** in an **ESS** (same SSID across multiple APs, [[L11 - Wireless Devices and Technologies]]):
```
Client moves away from AP1 ─▶ signal drops ─▶ client sends REASSOCIATION to AP2
                                                 └─ AP2/controller acknowledges; traffic now delivered via AP2
                                                    session state (keys, IP) preserved → seamless
```
- **Proper roaming** = **reassociation**: the controller hands off context, so ongoing sessions (calls, SSH) survive.
- **Misconfigured roaming:** the client does a fresh **association** (and full 802.1X re-authentication) instead, so **ongoing sessions drop or freeze**. Common causes: APs with different SSID/security settings, autonomous APs without a controller, VLAN mismatch between APs (the client gets a new IP and every TCP session dies).
- **Sticky client:** the client stubbornly holds on to a distant AP with a weak signal instead of roaming. **The client decides when to roam**, so tuning options include minimum RSSI / data-rate thresholds and 802.11k/v.

**Disassociation:** the controller or AP **tells a client to leave** (load balancing, band steering, a better AP is available). If the client doesn't roam cleanly, users see brief drops. Note that Wi-Fi conditions change constantly as people and devices move.

**Fast-roaming standards (beyond the lecture, worth knowing):**
| Standard | Purpose |
|----------|---------|
| **802.11k** | AP gives the client a **neighbor report** (list of nearby APs), so it finds a roam target faster |
| **802.11v** | **BSS transition management**: the AP *suggests* the client move (a polite alternative to forced disassociation) |
| **802.11r** | **Fast BSS Transition (FT)**: pre-authenticates keys so a WPA2/3-Enterprise roam takes < 50 ms instead of a full 802.1X exchange. Essential for VoIP over Wi-Fi |

> **Security note:** Unexpected *mass* disassociations/deauthentications can also be a **deauth attack** (spoofed management frames, often the setup for an evil twin, [[L19 - Network Attacks]]). **802.11w** (Protected Management Frames, mandatory in WPA3) prevents it.

---

## Symptom → Cause Cheat Sheet

| Symptom | Most likely cause |
|---------|-------------------|
| Wi-Fi fine with 5 users, unusable with 40 in the same room | **Contention / insufficient capacity** (CSMA/CA airtime) |
| Whole AP slow since one old device joined | **Legacy low data rate** clients hogging airtime |
| Office internet slow every morning, LAN fast | **Bottleneck** at the WAN/internet link |
| 1 Gbps port, only ~200 Mbps delivered | **Contracted bandwidth** (CIR) vs interface speed |
| VoIP: people talk over each other | **Latency** |
| VoIP: choppy / robotic | **Jitter** |
| VoIP: gaps / missing words | **Packet loss** |
| Calls bad only during backups / large transfers | **No QoS**: voice not prioritized |
| Wi-Fi drops when the break-room microwave runs | **2.4 GHz interference** |
| Good signal, poor performance, high retries | **Interference / low SNR** (high noise floor), co-channel |
| Weak signal in a far corner / behind elevator shaft | **Signal degradation / insufficient coverage** |
| Calls drop when walking between floors | **Roaming misconfiguration** (no reassociation / 802.11r, VLAN mismatch) |
| Laptop clings to a far AP with poor speed | **Sticky client** |
| Many clients kicked off at once, repeatedly | Aggressive load-balancing disassociation, or a **deauth attack** |

---

## Key Terms

| Term | Definition |
|------|-----------|
| Congestion / contention | Too many devices competing for shared bandwidth or airtime |
| CSMA/CD | Collision detection (half-duplex wired Ethernet) |
| CSMA/CA | Collision avoidance (Wi-Fi): listen, back off, then send |
| Airtime | Share of channel time a client uses; slow clients use more |
| Minimum/basic data rate | Lowest rate an AP accepts; raising it removes legacy clients |
| Bottleneck | Narrowest shared link in a path |
| Bandwidth | Maximum link capacity (technology or contract) |
| Throughput | Actual achieved data rate |
| CIR | Committed information rate: contracted carrier bandwidth |
| QoS | Prioritizing traffic classes to meet latency/jitter/loss needs |
| Latency | One-way (or round-trip) delay; VoIP target ≤ 150 ms one-way |
| Jitter | Variation in packet arrival delay; VoIP target < 30 ms |
| Packet loss | Dropped packets; VoIP target < 1% |
| Jitter buffer | Receiver buffer that smooths arrival variation |
| RTP | Real-time Transport Protocol (VoIP media over UDP) |
| DSCP EF | Expedited Forwarding marking (46) for voice |
| RF interference | Other transmitters in the same band |
| Noise floor / SNR | Background RF level / signal minus noise (want ≥ 25 dB) |
| RSSI | Received signal strength (dBm); −67 dBm for voice |
| Heat map | Visual survey of signal coverage |
| Site survey | Planning/validation of AP placement |
| Roaming | Moving between APs in an ESS via reassociation |
| Reassociation | Roaming handoff preserving session context |
| Disassociation | AP/controller instructing a client to leave |
| Sticky client | Client refusing to roam from a weak AP |
| 802.11k / v / r | Neighbor reports / BSS transition / fast transition |
| 802.11w | Protected management frames (stops deauth attacks) |

---

## Real-World Connections

**k3s lab:**
- **Bandwidth vs throughput on your own nodes:**
  ```bash
  kubectl run iperf-server --image=networkstatic/iperf3 -- -s
  kubectl run iperf-client --rm -it --image=networkstatic/iperf3 -- -c <server-pod-ip> -t 10
  ```
  Compare pod-to-pod throughput with the node-to-node baseline. The gap is **VXLAN encapsulation overhead + CPU** (ARM SBCs often bottleneck on CPU, not the NIC). That's "throughput < bandwidth" in your own cluster.
- **Latency/jitter from inside the cluster:** `ping -c 100 <node>` then read `mdev` in the summary. That's jitter.
- **Elasticsearch bulk indexing** competing with Kibana dashboard queries is a QoS problem at the application layer. Kubernetes has no packet-level QoS by default, but pod **QoS classes** (Guaranteed / Burstable / BestEffort) and `kubernetes.io/egress-bandwidth` annotations (bandwidth CNI plugin) are the cluster equivalents.
- **Kubeshark** shows per-request latency, which is the L7 view of the same metric.

**AWS/GCP:**
- **Bandwidth is contractual in the cloud too:** EC2 instance types have bandwidth caps ("Up to 12.5 Gbps" = burstable via credits, then throttled to baseline). `ethtool -S` → `bw_out_allowance_exceeded` reveals **cloud bottlenecking**.
- A single flow is limited to **5 Gbps** across AZs / **10 Gbps** in a placement group. One TCP stream won't reach the instance's full bandwidth, so use parallel streams (`iperf3 -P 8`).
- **Latency:** same-AZ < 1 ms, cross-AZ ~1–2 ms, cross-region tens to 100+ ms. Architect real-time components within one region.
- **CloudWatch Network Monitor** / VPC Flow Logs track loss and latency for hybrid links.

**Enterprise:** A voice-ready Wi-Fi design target is commonly −67 dBm RSSI, 25 dB SNR, < 1% retries, 802.11r/k/v enabled, and 1/2/5.5/11 Mbps rates disabled. QoS trust boundary at the IP phone port, DSCP EF in LLQ across the WAN.

**Telecom/5G:** QoS is native in 5G as **5QI** (QoS identifiers with defined packet delay budgets, e.g., 5QI 1 = conversational voice, 100 ms PDB). V2X sidelink safety messages need ~10–100 ms latency with very high reliability. CSMA-like contention is exactly why C-V2X uses **sensing-based semi-persistent scheduling** (mode 2) instead of pure random access.

---

## Troubleshooting Scenario

**Scenario:** Users report that Teams/Zoom calls "break up" every afternoon. Web browsing feels fine. The branch has a 1 Gbps Ethernet handoff to the ISP with a **200 Mbps contract**. NetFlow shows that between 14:00 and 16:00 a nightly-turned-daily cloud backup job pushes ~190 Mbps upstream. Ping to the call provider during that window: average 85 ms, **mdev (jitter) 48 ms**, **3% loss**. Outside the window: 22 ms, 2 ms jitter, 0% loss.

**Analysis ([[L21 - Network Troubleshooting Methodology]]):**
1. **Identify:** calls only, afternoon only, browsing OK (TCP tolerates delay). **What changed:** the backup job's schedule moved.
2. **Theory:** WAN **bottleneck** (200 Mbps contract) saturated by the backup. No QoS, so voice queues behind bulk traffic, causing **jitter + loss**.
3. **Test:** the metrics above, correlated with NetFlow top talkers. Confirmed.
4. **Plan:** (a) move the backup back to nights, (b) **shape** backup traffic to ≤ 100 Mbps, (c) configure **QoS**: mark voice DSCP EF, put it in a priority queue on the WAN egress, shaped to the 200 Mbps contract (not 1 Gbps, or the queue builds at the ISP instead of on your router, where you can't control it).
5. **Implement** via change management.
6. **Verify:** jitter < 30 ms, loss < 1% during a test backup. **Preventive:** alert on WAN utilization > 80% and on voice MOS score.
7. **Document.**

**Key insight:** **Shape to the contracted rate.** QoS only works if *your* device is where the congestion queue forms.

---

## Hands-On Ideas

1. **Bandwidth vs throughput:** `iperf3 -c <host>` between two nodes (TCP), then `iperf3 -c <host> -u -b 50M` (UDP) and read the **jitter** and **lost/total** columns. That's what VoIP experiences.
2. **Measure jitter and loss:** `ping -c 200 -i 0.2 1.1.1.1` and read `mdev` + loss %. Start a large download simultaneously and watch both rise (bufferbloat).
3. **Bufferbloat test:** Run the Waveform bufferbloat test in a browser. A high "loaded latency" grade means your router queues too much, so enable **SQM (fq_codel/cake)** on OpenWrt/OPNsense.
4. **Wi-Fi survey:** Use a Wi-Fi analyzer app (or `sudo iw dev wlan0 scan | grep -E 'SSID|signal|freq'` / `nmcli dev wifi list`) to map channels and RSSI around your space. Note which 2.4 GHz networks overlap 1/6/11.
5. **Roaming test:** Walk between two APs on a call while running `ping -i 0.2` to the gateway. Count lost pings during the handoff (802.11r should keep it under 1–2).

---

## Quiz

1. Why does a Wi-Fi network slow down noticeably as more clients join a single AP?
   - A) Wi-Fi uses CSMA/CD  B) Each client reduces the AP's bandwidth rating  C) DHCP exhaustion  D) Clients share airtime on a half-duplex medium (CSMA/CA)

2. One legacy 802.11b scanner joins an AP, and every client's throughput drops. What's the best fix?
   - A) Disable low (1/2/5.5/11 Mbps) data rates  B) Increase TX power  C) Switch to 2.4 GHz only  D) Enable PortFast

3. A branch's router port shows 1000 Mb/s, but speed tests never exceed 200 Mbps. What's most likely?
   - A) Duplex mismatch  B) Jitter  C) Contracted (CIR) bandwidth is 200 Mbps  D) CSMA/CA

4. Users say VoIP calls sound choppy and robotic. Which metric is most likely out of spec?
   - A) Latency  B) Jitter  C) Bandwidth  D) RSSI

5. What is the commonly cited maximum one-way latency for acceptable VoIP?
   - A) 15 ms  B) 50 ms  C) 150 ms  D) 500 ms

6. Why does VoIP not retransmit lost packets?
   - A) Late voice packets are useless, so RTP/UDP skips them  B) It uses TCP  C) QoS forbids it  D) Jitter buffers retransmit instead

7. Calls are fine except during large file transfers. What's the best long-term fix?
   - A) Disable the jitter buffer  B) Implement QoS: mark voice EF and prioritize it  C) Replace Cat6 cabling  D) Use 2.4 GHz

8. Wi-Fi in the break room drops whenever lunch starts. What's the likely cause?
   - A) Microwave oven interference in 2.4 GHz  B) DHCP lease expiry  C) STP reconvergence  D) Duplex mismatch

9. Which 2.4 GHz channels are non-overlapping in North America?
   - A) 1, 5, 9  B) 1, 6, 11  C) 2, 7, 12  D) 36, 40, 44

10. Signal is −62 dBm, but performance is poor and retries are high. The noise floor is −70 dBm. What is the problem?
    - A) Insufficient coverage  B) Sticky client  C) Bandwidth cap  D) Low SNR (8 dB) from interference

11. Which band best penetrates walls and covers the most distance?
    - A) 2.4 GHz  B) 5 GHz  C) 6 GHz  D) 60 GHz

12. VoIP calls drop every time a user walks between two APs on the same SSID. Each AP is configured independently with different VLANs. What's the problem?
    - A) Interference  B) Roaming misconfiguration: no seamless reassociation, and the client changes subnet  C) Jitter  D) Microwave

13. A laptop stays connected to a distant AP with a weak signal while a closer AP is available. What is this?
    - A) Sticky client  B) Evil twin  C) Channel overlap  D) Disassociation attack

14. Which standard enables fast, pre-authenticated roaming for WPA2/WPA3-Enterprise voice clients?
    - A) 802.11k  B) 802.11v  C) 802.11r  D) 802.11w

**Answers:** 1-D, 2-A, 3-C, 4-B, 5-C, 6-A, 7-B, 8-A, 9-B, 10-D, 11-A, 12-B, 13-A, 14-C

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[L04 - Ports, Protocols & Services]] — Ports/protocols (RTP/UDP, SIP 5060/5061)
- [[L06 - Network Topologies, Architectures & Traffic Flows]] — Traffic flows (north-south / east-west)
- [[L08 - Modern Networks]] — SD-WAN (application-aware path selection for latency/jitter)
- [[L11 - Wireless Devices and Technologies]] — Wireless standards, channels, bands, ESS, controllers
- [[L14 - Network Monitoring]] — NetFlow, baselines, performance monitoring
- [[L19 - Network Attacks]] — Deauth attacks / evil twin
- [[L21 - Network Troubleshooting Methodology]] — Troubleshooting methodology
- [[L22 - Troubleshooting Cabling and Physical Issues]] — Duplex mismatch, CRC errors (causes of loss)
- [[certs/network-plus]] — Domain 5.4 objective
