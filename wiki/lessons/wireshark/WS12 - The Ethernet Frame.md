---
id: WS12
aliases: [WS12]
title: The Ethernet Frame
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Module 3 - Anatomy of a Packet (module name to confirm)
date: 2026-11-12
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Lab 1-IntrotoWireshark.pcapng
tags: [wireshark, ethernet, mac-address, ethertype, fcs, mtu, oui, layer-2]
---

# WS12 — The Ethernet Frame

> 🎛️ **Interactive visualization:** [Packet Dissector](../../visualizations/wireshark/WS11-13%20-%20Packet%20Dissector.html): click any byte of Lab 1 frame 1 to see which field it belongs to and what it means. Opens in your browser.

## Summary
The Ethernet II header is **14 bytes**:
| Bytes | Field | Notes |
|-------|-------|-------|
| 6 | **Destination MAC (DMAC)** | Who it's for |
| 6 | **Source MAC (SMAC)** | The station that sent this frame |
| 2 | **Type (EtherType)** | Which protocol comes next |

Then come **46–1500 bytes of payload** (the IP header + data; 1500 is the standard MTU), and finally a **4-byte FCS** (frame check sequence: a CRC checksum).

**Common EtherTypes:** `0x0800` = IPv4 · `0x0806` = ARP · `0x86DD` = IPv6. Wireshark reads the type and "gets out the dictionary" (dissector) for the next protocol.

**FCS:** the receiver (usually a switch) recalculates the CRC. If it doesn't match, the frame is **dropped**, never forwarded. The instructor's analogy is a packing list that says 20 laptops when the box holds 19. Causes are **physical/link**: bad cables, transceivers, duplex problems.

**You won't see FCS errors in Wireshark.** The NIC checks the FCS and **strips** it before handing the frame to the capture driver, and frames with a bad FCS are dropped before capture. Look for FCS/CRC errors in **switch and router interface counters**, not in packet captures.

---

## Core Concepts

### Lab 1, frame 1, byte by byte (verified with tshark)
```
94 10 3e 9d 02 73 | a4 5e 60 e7 ce 6d | 08 00 | 45 00 00 3c ...
   destination MAC   |    source MAC     | type  | IPv4 header begins
```
| Field | Value | Wireshark shows |
|-------|-------|-----------------|
| Destination | `94:10:3e:9d:02:73` | `BelkinIntern_9d:02:73`, the home router |
| Source | `a4:5e:60:e7:ce:6d` | `Apple_e7:ce:6d`, the client laptop |
| Type | `0x0800` | IPv4 |
| LG / IG bits | 0 / 0 | Globally unique (factory) address / individual (unicast) |
| FCS | — | Not present (stripped by the NIC) |

**OUI:** the first 3 bytes of a MAC identify the vendor (`94:10:3e` = Belkin, `a4:5e:60` = Apple). Wireshark's name resolution swaps them in (View → Name Resolution → Resolve Physical Addresses).

### Frame size math
| Item | Bytes |
|------|-------|
| Ethernet header | 14 |
| Payload (IP + above) | 46 min · 1500 max (MTU) |
| FCS | 4 (not captured) |
| Captured frame size range | 60–1514 (64–1518 on the wire with FCS) |

Lab 1: frame 1 is **74 bytes** = 14 Ethernet + 60 IP. The largest frame is 1484 bytes. Data bigger than the MTU is split by **upper layers** (TCP segmentation) before Ethernet ever sees it.

> **Lecture vs the full picture:**
> - Frames shorter than 60 bytes get **padding** on the wire. Wireshark shows it as "Padding" or a trailer.
> - Some captures (TAPs, special NICs) **do** include the FCS. Wireshark then shows it, and it can validate it if enabled.
> - **802.1Q VLAN tags** add 4 bytes (TPID `0x8100`) between the source MAC and the EtherType. You'll see them on trunk-port SPAN captures.

---

## Key Terms
| Term | Definition |
|------|-----------|
| MAC address | 48-bit hardware address. OUI (vendor, 24 bits) + device part |
| EtherType | 2-byte next-protocol identifier |
| FCS | 4-byte CRC32 trailer for error detection |
| MTU | Max payload per frame. 1500 on standard Ethernet; jumbo frames ~9000 |
| IG / LG bits | Individual vs group (multicast/broadcast) / globally vs locally administered |

## Real-World Connections
- **GCP:** the VPC MTU defaults to **1460**, not 1500 (configurable up to 8896). Packet captures on GCP VMs show smaller max segments.
- **k3s:** Flannel VXLAN steals 50 bytes, so the pod MTU is 1450. A mismatched MTU looks like stalled large transfers in captures.
- **Network+:** cabling faults → CRC/FCS errors and runts on `show interface` ([[L22 - Troubleshooting Cabling and Physical Issues]]).

## Troubleshooting Scenario
**Scenario:** Users on one floor see slow, retrying file copies. Your Wireshark capture looks clean: no errors.
**Why:** frames with FCS errors never reach Wireshark. **Check the switch port counters** (`show interface` CRC / input errors) and replace the cable or transceiver. In the capture you'd only see the *effect*: TCP retransmissions.

## Hands-On Ideas
1. Lab 1 frame 1: expand Ethernet II, click each field, and watch the highlighted bytes in the bytes pane.
2. Add a column for `eth.type`, then filter `eth.type == 0x0806` (no ARP in Lab 1; try your own capture).
3. Capture on your own Mac and run `arp -a` to match MACs to vendors.

## Quiz
1. EtherType 0x86DD means the next protocol is:
   - A) IPv4  B) ARP  C) IPv6  D) VLAN
2. Why don't you see frames with FCS errors in Wireshark?
   - A) Wireshark hides them  B) The NIC drops them before capture and strips the FCS  C) They're encrypted  D) They're too small
3. How long is the Ethernet II header?
   - A) 8 bytes  B) 14 bytes  C) 20 bytes  D) 18 bytes
4. Where do you troubleshoot FCS errors?
   - A) Wireshark's Expert Info  B) Switch/router interface counters  C) DNS logs  D) The TTL column

**Answers:** 1-C, 2-B, 3-B, 4-B

## Weak Areas to Revisit
*(none flagged yet)*

## Linked Pages
- [[WS11 - The Anatomy of a Packet]] · [[WS13 - The IP Header]] · [[network-devices]] · [[wireshark]] · [[certs/wireshark-101]]
