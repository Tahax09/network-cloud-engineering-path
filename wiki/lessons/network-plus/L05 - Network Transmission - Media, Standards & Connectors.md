---
id: L05
aliases: [L05]
title: Network Transmission — Media, Standards & Connectors
course: CompTIA Network+
cert_track: network-plus
objective: "1.5 — Network Transmission Media, Standards & Connectors"
date: 2025-05-28
status: Complete
quiz_attempted: No
weak_areas: []
tags: [wi-fi, 802.11, ethernet, fiber, cable, connectors, sfp, plenum, mimo, cat6]
---

# L05 — Network Transmission: Media, Standards & Connectors

## Summary
Know the 802.11 standards cold (especially what MIMO starts at and what 802.11ac drops). Know the T-rule for Ethernet. Know the 1000BASE-T 4-pair trap. Know single-mode vs multi-mode fiber by color and distance. Know plenum vs non-plenum by building location.

## 802.11 Wi-Fi Standards

| Standard | Wi-Fi Gen | Frequency | Max Speed | Key Tech |
|----------|----------|-----------|-----------|---------|
| 802.11a | — | 5 GHz | 54 Mbps | OFDM |
| 802.11b | — | 2.4 GHz | 11 Mbps | DSSS |
| 802.11g | — | 2.4 GHz | 54 Mbps | OFDM |
| 802.11n | Wi-Fi 4 | 2.4 / 5 GHz | 600 Mbps | **MIMO** |
| 802.11ac | Wi-Fi 5 | **5 GHz only** | 3.5 Gbps | MU-MIMO |
| 802.11ax | Wi-Fi 6/6E | 2.4 / 5 / 6 GHz | 9.6 Gbps | OFDMA |

**Key facts:**
- MIMO starts at **802.11n** — multiple antennas transmit and receive simultaneously
- **802.11ac is 5 GHz only** — drops 2.4 GHz entirely
- 2.4 GHz = longer range, more congestion. 5 GHz = faster, shorter range. 6 GHz = Wi-Fi 6E only

## 2.4 GHz Channels
Each channel = 22 MHz bandwidth; channels are 5 MHz apart → most overlap.

**Non-overlapping channels: 1, 6, 11 only** (US). Use these when deploying multiple APs in the same area. Channels 1 and 3 overlap — they interfere with each other.

## Wireless Modes

| Term | Description |
|------|-------------|
| Infrastructure mode | AP connects clients to wired LAN |
| Ad-hoc mode | Direct device-to-device, no AP |
| BSS | Basic Service Set — one AP, one cell |
| ESS | Extended Service Set — multiple APs, same SSID, roaming |
| Mesh | APs relay wirelessly through each other |
| Lightweight AP | Managed by WLC; no standalone operation |
| Autonomous AP | Self-contained, home/SMB |

## 802.3 Wired Ethernet — The T Rule
**If the standard ends in `-T` → copper UTP, max 100m, RJ-45. Every time.**

| Standard | Name | Speed | Min Cable | Max Distance |
|----------|------|-------|-----------|-------------|
| 802.3 | 10BASE-T | 10 Mbps | Cat3 | 100m |
| 802.3u | 100BASE-TX | 100 Mbps | Cat5 | 100m |
| 802.3ab | 1000BASE-T | 1 Gbps | Cat5e | 100m |
| 802.3an | 10GBASE-T | 10 Gbps | Cat6a (Cat6=55m) | 100m |
| 802.3bq | 40GBASE-T | 40 Gbps | Cat8 | 30m |

**Critical exam trap:** 1000BASE-T requires **all 4 cable pairs** (8 wires). Older Cat5 installs often connected only 2 pairs for 100 Mbps — upgrading to gigabit fails because pins 4,5,7,8 are open. Test all 8 pins with a cable tester.

## Cable Categories

| Category | Max Speed | Distance | Notes |
|----------|-----------|----------|-------|
| Cat5 | 100 Mbps | 100m | Legacy |
| Cat5e | 1 Gbps | 100m | Most common existing |
| Cat6 | 10 Gbps (55m only) | 100m @ 1G | Common new offices |
| Cat6a | 10 Gbps | 100m | Full 10G at 100m |
| Cat7 | 10 Gbps+ | 100m | Shielded (STP) |
| Cat8 | 25–40 Gbps | 30m | Data center, short runs |

## Fiber Optic

Both SMF and MMF share the **same 125 μm outer cladding** — only the core differs.

| Feature | Single-mode (SMF) | Multi-mode (MMF) |
|---------|-------------------|-----------------|
| Core | ~9 μm (tiny) | 50–62.5 μm (large) |
| Max Distance | 100+ km | ~2 km |
| Cost | Higher | Lower |
| Jacket color | **Yellow** (OS1/OS2) | **Orange** (OM1–4) or **Aqua** (OM5) |
| Use | WAN, long-haul, DC backbone | Campus, short DC runs |

**Modal dispersion (MMF):** Multiple modes travel different paths → pulses spread → limits distance.

## Fiber Connectors

| Connector | Type | Key Characteristic |
|-----------|------|-------------------|
| ST | MMF | Round bayonet twist-lock; legacy |
| SC | SMF or MMF | Square push-pull; one fiber (need 2 for duplex) |
| LC | SMF or MMF | Small, push-pull; often duplex; **data center standard** |
| MPO/MTP | MMF | 8/12/24 fibers in one plug; high-density DC backbone |

## Transceivers

| Transceiver | Speed | Notes |
|------------|-------|-------|
| SFP | 1 Gbps | Small Form-factor Pluggable |
| SFP+ | 10 Gbps | Enhanced SFP; also used with DAC cable |
| QSFP/QSFP+ | 40 Gbps | Quad SFP (4×10G) |
| FC HBA | Varies | Fiber Channel Host Bus Adapter; SAN |

## DAC Cable
Direct Attach Copper — twinaxial cable with SFP+ connectors. For high-speed (10G/40G) short runs within a rack (≤7m). Cheaper than fiber transceivers for same-rack connections.

## Coaxial Connectors
- **F-type:** Threaded; cable TV, DOCSIS modems
- **BNC:** Bayonet twist-lock; legacy 10BASE-2, test equipment

## Plenum vs Non-Plenum
**Plenum space:** HVAC airspace above dropped ceilings, below raised floors.
- Requires **CMP-rated (plenum) cable** — low-smoke, low-toxicity
- Non-plenum (CMR/PVC) in plenum spaces = fire code violation
- Exam signal: "cable run above the ceiling tiles" → must be CMP

## Troubleshooting Scenario
> After upgrading to gigabit switches, one floor still shows 100 Mbps.

Cause: Floor was wired with Cat5, only 2 pairs punched (pins 1,2,3,6). 1000BASE-T needs all 4 pairs. Switch negotiates down to 100 Mbps.

Fix: Cable tester → verify all 8 pins. Re-punch patch panel for all 4 pairs, or replace cable with Cat5e/Cat6.

## Exam Traps
- MIMO starts at **802.11n**, not 802.11ac
- 802.11ac is **5 GHz only** — no 2.4 GHz
- Non-overlapping 2.4 GHz channels: **1, 6, 11** (not 1, 5, 9)
- **1000BASE-T needs all 4 pairs** — this breaks legacy 2-pair Cat5 installs
- SMF = yellow jacket. MMF = orange or aqua
- Plenum cable required in **HVAC airspace** — not just in walls

## Related
- [[concepts/network-media]] — cable and Wi-Fi reference
- [[L06 - Network Topologies, Architectures & Traffic Flows]] — physical vs logical topology
