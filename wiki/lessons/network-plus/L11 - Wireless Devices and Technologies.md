---
id: L11
aliases: [L11]
title: Wireless Devices and Technologies
course: CompTIA Network+
cert_track: network-plus
objective: "2.3 — Network Implementation: Wireless Devices and Technologies"
date: 2026-06-15
status: Complete
quiz_attempted: No
weak_areas: []
tags: [wireless, 802.11, wifi, ssid, wpa2, wpa3, mimo, antenna, channels]
---

# L11 — Wireless Devices and Technologies

## Summary

Wi-Fi (802.11) runs on 2.4 GHz, 5 GHz, and (newer) 6 GHz. It is half-duplex — all stations share a channel and cannot send/receive simultaneously. This lesson covers channel planning, SSID types, authentication (WPA2 vs WPA3), access point architectures (autonomous vs controller-based), and antenna patterns. Most of it is memorization for the exam.

---

## Core Concepts

### Wi-Fi Frequencies

| Band | Channel Width | Non-Overlapping Channels | Notes |
|------|--------------|--------------------------|-------|
| 2.4 GHz | 20 MHz | **1, 6, 11** (US limit: 11 channels) | Most interference; widest range |
| 5 GHz | 20–40 MHz | Non-overlapping by design | More channels, less interference |
| 6 GHz | 20–40 MHz+ | Non-overlapping | Highest throughput; smallest coverage area |

- 2.4 GHz channels overlap every 5 MHz — only 3 are truly non-overlapping (1, 6, 11)
- 5 GHz channels are non-overlapping from the start
- Wider channels = more throughput but more interference risk; best in **isolated/controlled environments**
- **Band steering**: controller automatically selects the best frequency/channel per client; small latency cost when roaming

### SSID & Service Sets

- **SSID** — the wireless network name; up to 32 alphanumeric characters; **case-sensitive**
- APs broadcast beacon frames announcing the SSID; can be disabled (but attackers can still find hidden SSIDs)

| Term | Meaning |
|------|---------|
| BSS (Basic Service Set) | Single AP covering a Basic Service Area |
| ESS (Extended Service Set) | Multiple APs sharing the same SSID; controller-based = true roaming |
| Guest network | Separate SSID on same AP; isolated from corporate traffic |
| Captive portal | Splash/login page required before guest network access; limits traffic until authenticated |

### Wireless Network Types

| Type | Description |
|------|-------------|
| Ad hoc | Device-to-device with no AP (IBSS) |
| Infrastructure | AP connected to wired network; most common home/corporate model |
| Point-to-point | Wireless bridge between two locations |
| Wireless mesh | APs route traffic between each other; route around failures; smarter than a plain ESS |

### Authentication & Encryption

#### WPA2 (IEEE 802.11i)
- Encryption: **AES-CCMP** (Counter Mode with Cipher Block Chaining Message Authentication)
- Four-way handshake + Pairwise Transient Key (PTK) for key exchange
- **WPA2-Personal (PSK):** shared passphrase; vulnerable to offline brute-force if handshake is captured
- **WPA2-Enterprise:** 802.1X + EAP; RADIUS server authenticates each user; only auth traffic flows until authenticated (like 802.1X on wired)

#### WPA3
- Replaces PSK with **SAE (Simultaneous Authentication of Equals)** — blocks offline dictionary attacks even if handshake is captured
- Encrypts traffic even on open networks (OWE — Opportunistic Wireless Encryption)
- Encryption: **AES-GCMP** (replaces AES-CCMP)
- **Easy Connect**: QR code device onboarding (replaces WPS push-button, which had security flaws)

| Feature | WPA2 | WPA3 |
|---------|------|------|
| Encryption cipher | AES-CCMP | AES-GCMP |
| Personal auth | PSK (passphrase) | SAE |
| Enterprise auth | 802.1X + EAP | 802.1X + EAP (enhanced) |
| Open network encryption | None | OWE |
| Device onboarding | WPS (flawed) | Easy Connect (QR) |

### Access Point Types

| Type | Description |
|------|-------------|
| Autonomous AP | Standalone; configured independently; no central controller |
| Lightweight AP (controller-based) | Managed by a WLAN controller (CAPWAP protocol); centralized config, true roaming, band steering |

Controller-based systems give the controller visibility into all APs — enables decisions about where clients connect and when to hand off.

### Antennas

| Type | Pattern | Use Case |
|------|---------|----------|
| Omnidirectional | 360° horizontal | Homes, offices; general coverage |
| Directional (Yagi, parabolic) | Focused beam | Long hallways, warehouses, building-to-building links |

- **MIMO (Multi-Input Multi-Output)** — introduced in 802.11n; multiple antenna chains used in parallel; significantly increases throughput
- 802.11n → 802.11ac (Wi-Fi 5) → 802.11ax (Wi-Fi 6) all use MIMO; each generation adds more streams

---

## Key Terms

| Term | Definition |
|------|-----------|
| SSID | Wireless network name (≤32 chars, case-sensitive) |
| BSS | Basic Service Set — single AP coverage area |
| ESS | Extended Service Set — multiple APs, same SSID, true roaming |
| Captive portal | Redirect to auth page before granting guest network access |
| WPA2-Personal | PSK-based auth; vulnerable to offline brute force |
| WPA2-Enterprise | 802.1X + EAP + RADIUS; per-user authentication |
| WPA3 / SAE | Simultaneous Authentication of Equals; prevents offline attacks |
| AES-CCMP | WPA2 encryption standard |
| AES-GCMP | WPA3 encryption standard (stronger) |
| MIMO | Multiple antennas for parallel streams; 802.11n+ |
| Autonomous AP | Standalone, self-configured AP |
| Lightweight AP | AP managed by central WLAN controller |
| Band steering | Controller auto-selects best band/channel per client |
| Channels 1, 6, 11 | Only non-overlapping channels in 2.4 GHz (US) |

---

## Real-World Connections

**k3s lab:** Your k3s nodes connect over your home Wi-Fi or wired. If you're on 2.4 GHz with congestion, you see exactly why non-overlapping channels matter — neighbor interference degrades UDP-heavy traffic (Kubeshark packet captures will show retransmits).

**Enterprise Wi-Fi:** Controller-based (Cisco WLC, Aruba) with lightweight APs maps directly to how AWS manages its internal RF infrastructure. WPA2-Enterprise + RADIUS is what any corporate SSO Wi-Fi uses — the same 802.1X framework that protects wired ports.

**Security angle:** WPA2-PSK's weakness is the captured 4-way handshake being cracked offline (hashcat). WPA3/SAE eliminates this because each session uses a different derived key even with the same passphrase.

---

## Troubleshooting Scenario

**Symptom:** Users on floor 3 report intermittent Wi-Fi drops and slow speeds, but floor 2 is fine. Both floors use the same SSID.

**Diagnosis path:**
1. Check which APs serve floor 3 — are they autonomous or controller-based?
2. If autonomous, clients may not be roaming properly (sticky clients holding onto weak signal)
3. Run a Wi-Fi analyzer — check what channel floor 3 APs are on and whether adjacent APs (or neighbors in adjoining building) are on the same channel
4. If 2.4 GHz: verify APs are on 1, 6, or 11 and not overlapping
5. Check for microwave ovens or Bluetooth devices causing 2.4 GHz interference
6. If controller-based: check band steering config and whether APs are over-subscribed

**Fix:** Assign non-overlapping channels to adjacent APs; reduce TX power to limit interference radius; if autonomous, migrate to controller for proper roaming.

---

## Hands-On Ideas

1. Run `iwconfig` or `iw dev` on Ubuntu — check your interface's mode, frequency, and TX power
2. Use `nmcli device wifi list` — see SSIDs, signal strength, channel assignments, and security mode
3. Install a Wi-Fi analyzer app (Android: WiFi Analyzer) — visualize channel congestion in your environment
4. Configure a VLAN-backed guest SSID on a home AP if your router supports it (OpenWRT, UniFi)
5. Test WPA3 vs WPA2 on your devices — check `iw list | grep WPA` for capability flags

---

## Quiz

1. Which channels are non-overlapping in the 2.4 GHz band (US)?
   - A) 1, 5, 9
   - B) 1, 6, 11
   - C) 1, 7, 13
   - D) 2, 6, 10

2. A company wants per-user authentication on their Wi-Fi (no shared passphrase). What is the correct configuration?
   - A) WPA2-Personal with a strong PSK
   - B) WPA2-Enterprise with 802.1X and EAP
   - C) WPA3-Personal with SAE
   - D) WEP with open authentication

3. What does WPA3-SAE fix compared to WPA2-PSK?
   - A) Increases channel width
   - B) Prevents offline dictionary attacks against captured handshakes
   - C) Enables MIMO on older hardware
   - D) Removes the need for an SSID

4. What is the difference between an autonomous AP and a lightweight AP?
   - A) Autonomous supports 5 GHz; lightweight only 2.4 GHz
   - B) Autonomous is standalone with no central controller; lightweight is managed by a WLAN controller
   - C) Lightweight APs can only connect 5 clients; autonomous are unlimited
   - D) There is no functional difference

5. A user reports the wireless network in the warehouse is unreliable with a standard omnidirectional AP. What antenna type would improve coverage along the long aisle?
   - A) Parabolic dish
   - B) Yagi directional antenna
   - C) A second omnidirectional AP
   - D) MIMO antenna array

6. WPA3 replaces which encryption cipher from WPA2?
   - A) AES-CCMP → AES-GCMP
   - B) TKIP → AES-CCMP
   - C) DES → AES
   - D) RC4 → TKIP

**Answers:** 1-B, 2-B, 3-B, 4-B, 5-B, 6-A

---

## Weak Areas to Revisit

*(none flagged yet)*
