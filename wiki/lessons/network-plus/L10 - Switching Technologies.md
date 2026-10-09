---
id: L10
aliases: [L10]
title: Switching Technologies
course: CompTIA Network+
cert_track: network-plus
objective: "2.2 — Network Implementation: Switching Technologies"
date: 2026-06-14
status: Complete
quiz_attempted: No
weak_areas: []
tags: [switching, vlan, stp, spanning-tree, trunk, access-port, lacp, etherchannel, svi, duplex, mtu, jumbo-frames, rstp, portfast, bpdu-guard]
---

# L10 — Switching Technologies

## Summary
Layer 2 switches forward traffic based on MAC address tables. VLANs logically segment a physical switch into isolated broadcast domains. Trunk ports carry multi-VLAN traffic using 802.1Q tags. Link aggregation (LACP/EtherChannel) bonds multiple physical links into one logical link. Spanning Tree Protocol (STP) prevents broadcast storms from Layer 2 loops by electing a root bridge and blocking redundant paths. RSTP (802.1W) adds faster convergence and PortFast/BPDU Guard harden edge ports.

---

## Core Concepts

### 1. How Layer 2 Switching Works

A switch builds a **MAC address table** by watching incoming traffic — it learns which MAC address arrived on which port. When forwarding, it looks up the destination MAC and sends only to that port (unicast). If the MAC is unknown, it floods out all ports in the VLAN.

**Key rule:** Switches operate at Layer 2 — they only look at MAC addresses, never IP addresses (unless it's an L3 switch doing routing).

```
# Cisco: view the MAC address table
show mac address-table

# Output columns: VLAN | MAC Address | Type | Port
```

---

### 2. VLANs

VLANs partition one physical switch into multiple isolated broadcast domains. Ports in VLAN 10 cannot communicate with ports in VLAN 20 without a router or L3 switch.

**VLAN database** — the switch's internal record of all configured VLANs and their assigned ports:

```bash
# Create a VLAN and name it
vlan 10
  name Engineering

vlan 20
  name Marketing

# Verify
show vlan
show vlan brief
```

**Port types:**

| Port Mode | Carries | Use Case |
|-----------|---------|---------|
| **Access** | Single VLAN | End devices (PCs, printers, phones) |
| **Trunk** | All VLANs (or allowed subset) | Switch-to-switch, switch-to-router links |

```bash
# Configure access port
interface Gi0/1
  switchport mode access
  switchport access vlan 10
  switchport voice vlan 20      # optional: separate voice VLAN

# Configure trunk port
interface Gi0/2
  switchport mode trunk
```

**802.1Q tagging:** On trunk ports, each frame gets a 4-byte VLAN tag inserted into the Ethernet header identifying its VLAN. Access ports strip/add the tag transparently — end devices never see it.

**Native VLAN:** Frames on the native VLAN cross a trunk port *untagged*. Default = VLAN 1. Both sides of a trunk must agree on native VLAN or you get a mismatch (security risk + STP issues).

---

### 3. Switched Virtual Interface (SVI)

A switch doesn't assign IPs to physical ports. Instead it uses **SVIs** — logical VLAN interfaces — for:
- Switch management (SSH/Telnet in)
- L3 switch routing (default gateway for hosts in that VLAN)

```bash
interface vlan 10
  ip address 192.168.10.1 255.255.255.0
  no shutdown
```

The SVI "listens" on all physical ports assigned to that VLAN — it's not tied to a single port.

---

### 4. Speed, Duplex & MTU

**Speed/duplex auto-negotiation** works when both sides are set to auto. Problems arise when one side is hardcoded and the other is auto (or hardcoded differently):

| Mismatch | Symptom |
|----------|---------|
| Speed mismatch | Interface goes down (encoding incompatible) |
| Duplex mismatch | Late collisions on the half-duplex side; FCS errors on full-duplex side |

```bash
# Manual config
interface Gi0/1
  speed 1000
  duplex full

# Diagnose errors
show interfaces Gi0/1
# Look for: late-collision, CRC, runts, giants
```

**Half-duplex vs Full-duplex:**
- **Half-duplex** — send OR receive at a time (legacy bus Ethernet, hubs)
- **Full-duplex** — simultaneous send and receive on separate wire pairs (all modern switched Ethernet)

**MTU:**

| Frame Type | Size |
|-----------|------|
| Standard Ethernet | 64 – 1518 bytes |
| Jumbo frame | Up to 9000 bytes |

Jumbo frames reduce overhead for high-throughput workloads (SANs, storage traffic) by sending larger payloads per frame. Must be enabled end-to-end — every switch and NIC in the path must support it.

---

### 5. Link Aggregation (EtherChannel)

Bonds multiple physical links into one logical port channel — increases bandwidth and provides redundancy.

```
4 × 10 Gbps links → 1 × 40 Gbps logical EtherChannel
```

**Protocols:**

| Protocol | Standard | Mode Options |
|----------|----------|-------------|
| **PAGP** | Cisco proprietary | auto / desirable |
| **LACP** | IEEE 802.3ad (open) | passive / active |

LACP active/active or active/passive forms the bundle. Passive/passive = never forms.

```bash
# LACP config (Cisco)
interface range Gi0/1 - 4
  channel-group 1 mode active    # LACP active

interface port-channel 1
  switchport mode trunk           # configure on the logical interface
```

**EtherChannel requirements — all member interfaces must match:**
- Same speed and duplex
- Same mode (all access or all trunk)
- Same access VLAN (if access) or same allowed VLAN list (if trunk)
- Same native VLAN
- Same STP settings

Configure members first with defaults, assign to channel-group, then configure everything on the port-channel interface — settings roll down.

---

### 6. Spanning Tree Protocol (STP)

**Problem:** Redundant switch links = Layer 2 loops. Broadcasts loop infinitely (no TTL at L2) → broadcast storm → network failure.

**Solution:** STP (IEEE 802.1D) logically blocks one port in every loop, creating a loop-free tree. If a link fails, it unblocks the standby port.

#### STP Election Process

**Step 1 — Elect the Root Bridge:**
- Bridge ID = **Priority (default 32768)** + **MAC address**
- Lowest Bridge ID wins → becomes root bridge
- All root bridge ports = **Forwarding**
- To control which switch is root: `spanning-tree vlan 10 priority 4096` (lower = wins)

**Step 2 — Each non-root switch elects a Root Port:**
- Port with lowest **cost to root** = root port (forwarding)
- Cost is based on link speed (higher speed = lower cost)

**Step 3 — Designate ports on each segment:**
- For each link between switches, the switch with the lower cost to root wins → its port = **Designated** (forwarding)
- The other switch's port = **Blocking** (discards traffic, still listens for BPDUs)

**Bridge ID tiebreaker:** If cost to root is equal, lower Bridge ID wins.

#### Port States (802.1D)

| State | Forwards Data? | Learns MACs? |
|-------|---------------|-------------|
| Blocking | No | No |
| Listening | No | No |
| Learning | No | Yes |
| Forwarding | Yes | Yes |
| Disabled | No | No |

**Problem with 802.1D:** Slow convergence — transitions through Listening → Learning → Forwarding can take 30–50 seconds.

#### RSTP (802.1W — Rapid Spanning Tree)

Modern replacement for 802.1D. Much faster convergence (< 1 second in most cases).

| Port Role | 802.1D | 802.1W |
|-----------|--------|--------|
| Root port | Root | Root |
| Forwarding | Designated | Designated |
| Blocked | Non-designated | **Alternate** (pre-computed backup to root) |
| Redundant hub link | — | **Backup** |

#### PortFast and BPDU Guard

**PortFast:** Skips Listening/Learning, puts access port straight into Forwarding. Use only on ports connected to end devices (never switch-to-switch).

**BPDU Guard:** If a BPDU (spanning tree message) is received on a PortFast port, it **immediately shuts the port down** (err-disabled). Prevents someone from plugging in a rogue switch and disrupting STP.

```bash
# Enable globally (applies to all PortFast ports)
spanning-tree portfast default
spanning-tree portfast bpduguard default

# Or per-interface
interface Gi0/1
  spanning-tree portfast
  spanning-tree bpduguard enable
```

---

## Key Terms

| Term | Definition |
|------|-----------|
| MAC address table | Switch's record of which MAC is on which port |
| VLAN | Virtual LAN — logical broadcast domain on a physical switch |
| VLAN database | Switch's internal record of all VLANs and their port assignments |
| Access port | Switch port assigned to a single VLAN |
| Trunk port | Switch port carrying traffic for multiple VLANs, tagged with 802.1Q |
| 802.1Q | IEEE standard for VLAN tagging on trunk ports |
| Native VLAN | VLAN whose traffic crosses a trunk port untagged (default VLAN 1) |
| SVI | Switched Virtual Interface — logical IP interface per VLAN on a switch |
| Duplex mismatch | One side full-, one side half-duplex → late collisions, FCS errors |
| Jumbo frame | Ethernet frame > 1518 bytes (up to 9000); reduces overhead for storage traffic |
| EtherChannel | Logical bond of multiple physical links into one |
| LACP | Link Aggregation Control Protocol — IEEE 802.3ad open standard |
| PAGP | Port Aggregation Protocol — Cisco proprietary link aggregation |
| STP | Spanning Tree Protocol — prevents L2 loops by blocking redundant ports |
| Root bridge | Switch with the lowest Bridge ID; all its ports forward |
| Bridge ID | Priority (default 32768) + MAC address; determines root bridge election |
| Root port | Non-root switch's port closest to the root bridge |
| Designated port | Forwarding port on each segment; belongs to switch with lower cost to root |
| Blocking port | STP-blocked port on the redundant path |
| RSTP (802.1W) | Rapid STP — faster convergence, adds Alternate and Backup port roles |
| PortFast | Skips STP listening/learning; instant forwarding for end-device ports |
| BPDU Guard | Err-disables a PortFast port if a BPDU is received (rogue switch protection) |

---

## Real-World Connections

**k3s lab:** Your Flannel CNI creates VXLAN overlays — conceptually similar to VLANs but at L3. Run `bridge fdb show` on a k3s node to see the equivalent of a MAC address table. Your Linux bridge (`cni0`) is a software switch doing exactly what this lesson describes.

**AWS VPC:** VLANs → Subnets. Each subnet is an isolated broadcast domain. Security Groups + NACLs replace STP-based isolation. Elastic Network Adapters use link aggregation concepts internally for high throughput instances.

**Enterprise:** Distribution layer switches run RSTP + EtherChannel on uplinks to core. Access layer ports use PortFast + BPDU Guard. SVIs on distribution switches are the default gateways for each VLAN/subnet.

---

## Troubleshooting Scenario

**Scenario:** After adding a second uplink between two switches for redundancy, the network becomes sluggish and users report intermittent connectivity. Broadcasts seem to be flooding the network.

**Root cause:** L2 loop — STP not configured/converged, or a new switch was plugged in that disrupted the STP topology.

**Debug steps:**
1. `show spanning-tree` — check port states; is anything in Listening/Learning for too long?
2. Look for a port that should be Blocking but is Forwarding on both sides
3. `show interfaces` — check for rapidly incrementing broadcast/collision counters
4. Verify BPDU Guard: `show interfaces status err-disabled` — any ports shut down?
5. Check EtherChannel if used: `show etherchannel summary` — members must show as bundled (P flag)
6. Verify native VLAN matches on both sides of trunk: `show interfaces trunk`

---

## Hands-On Ideas

- `bridge fdb show` on k3s node — see the software MAC address table
- `ip link show type bridge` — inspect the Linux bridge acting as a switch
- Configure VLANs in GNS3 or Packet Tracer — trunk between two switches, verify 802.1Q tags with Wireshark
- Simulate a broadcast storm in a lab: connect two switch ports together without STP and watch `show interfaces` counters spike
- Test LACP: bond two NICs on Ubuntu with `ip link add bond0 type bond mode 802.3ad`

---

## Quiz

**Q1.** A switch port is configured as a trunk. Frames arrive without a VLAN tag. Which VLAN will the switch assign them to?
- A) VLAN 1 (default)
- B) The highest-numbered VLAN
- C) The native VLAN
- D) They are dropped

**Q2.** A network has redundant switch links and no STP. Users report the network is completely unresponsive. What is the most likely cause?
- A) Duplex mismatch
- B) Broadcast storm from a Layer 2 loop
- C) MTU mismatch causing fragmentation
- D) VLAN misconfiguration

**Q3.** Which STP port role is pre-computed as the backup path to the root in RSTP?
- A) Root port
- B) Designated port
- C) Alternate port
- D) Backup port

**Q4.** All switches have the default STP priority. Which switch becomes the root bridge?
- A) The switch with the highest MAC address
- B) The switch with the lowest MAC address
- C) The switch with the most ports
- D) The switch configured manually as root

**Q5.** What must be true of all interfaces in an EtherChannel bundle?
- A) They must be on different VLANs
- B) They must have matching speed, duplex, mode, and VLAN settings
- C) They must use PAGP — LACP is not supported
- D) They must be trunk ports only

**Q6.** PortFast is enabled on a switch port and a new switch is connected to it. BPDU Guard is also enabled. What happens?
- A) STP renegotiates normally
- B) The port immediately enters forwarding state
- C) The port is err-disabled upon receiving a BPDU
- D) The new switch becomes the root bridge

**Answers:** Q1-C, Q2-B, Q3-C, Q4-B, Q5-B, Q6-C

---

## Weak Areas to Revisit
*(none flagged yet)*
