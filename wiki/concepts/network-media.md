# Network Media, Standards & Connectors

## 802.11 Wi-Fi Standards
| Standard | Wi-Fi | Frequency | Speed | Key |
|----------|-------|-----------|-------|-----|
| 802.11a | — | 5 GHz | 54 Mbps | OFDM |
| 802.11b | — | 2.4 GHz | 11 Mbps | DSSS |
| 802.11g | — | 2.4 GHz | 54 Mbps | OFDM |
| 802.11n | Wi-Fi 4 | 2.4/5 GHz | 600 Mbps | **MIMO** |
| 802.11ac | Wi-Fi 5 | **5 GHz only** | 3.5 Gbps | MU-MIMO |
| 802.11ax | Wi-Fi 6/6E | 2.4/5/6 GHz | 9.6 Gbps | OFDMA |

- MIMO starts at **802.11n**
- 802.11ac = **5 GHz only** (no 2.4 GHz)
- Non-overlapping 2.4 GHz channels: **1, 6, 11**

## 802.3 Ethernet — The T Rule
`-T` suffix = copper UTP = RJ-45 = max 100m. Always.

| Standard | Speed | Min Cable | Max Distance |
|----------|-------|-----------|-------------|
| 10BASE-T | 10 Mbps | Cat3 | 100m |
| 100BASE-TX | 100 Mbps | Cat5 | 100m |
| 1000BASE-T | 1 Gbps | Cat5e | 100m |
| 10GBASE-T | 10 Gbps | Cat6a (Cat6=55m) | 100m |
| 40GBASE-T | 40 Gbps | Cat8 | 30m |

**1000BASE-T needs all 4 cable pairs.** Older 2-pair installs fail to negotiate 1G.

## Cable Categories
| Cat | Speed | Distance |
|-----|-------|----------|
| Cat5 | 100 Mbps | 100m |
| Cat5e | 1 Gbps | 100m |
| Cat6 | 10 Gbps (55m) / 1G (100m) | 100m |
| Cat6a | 10 Gbps | 100m |
| Cat8 | 25–40 Gbps | 30m |

## Fiber
Both SMF and MMF: 125 μm outer cladding. Core size differs.

| | SMF | MMF |
|-|-----|-----|
| Core | ~9 μm | 50–62.5 μm |
| Distance | 100+ km | ~2 km |
| Color | **Yellow** | **Orange/Aqua** |
| Use | WAN, backbone | Campus, DC short |

## Fiber Connectors
| Connector | Type | Notes |
|-----------|------|-------|
| ST | MMF | Round bayonet; legacy |
| SC | Both | Square push-pull; one fiber |
| LC | Both | Small push-pull; **DC standard** |
| MPO/MTP | MMF | 8/12/24 fibers; high-density |

## Transceivers
| Type | Speed |
|------|-------|
| SFP | 1 Gbps |
| SFP+ | 10 Gbps |
| QSFP+ | 40 Gbps |

**DAC (Direct Attach Copper):** Twinaxial with SFP+ connectors. ≤7m. Cheaper than fiber for same-rack 10G/40G.

## Plenum
**Plenum space** = HVAC airspace (above dropped ceilings, below raised floors). Requires **CMP-rated** cable. Non-CMP in plenum space = fire code violation.

## Linked Lessons
- [[L05]] — full lesson
