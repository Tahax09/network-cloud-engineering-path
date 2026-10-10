---
id: WS02
aliases: [WS02]
title: Your First Packet Capture
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Lesson 1 - Packet Capture
date: 2026-10-10
status: Complete
quiz_attempted: No
weak_areas: []
tags: [wireshark, capture, interfaces, extcap, pcapng, pcap, save-as, capture-options, ring-buffer, dumpcap, intermittent-problems]
---

# WS02 — Your First Packet Capture

## Summary
This lesson covers going from launching Wireshark to a saved trace file. You **pick the interface that has traffic** (the sparkline), **double-click to capture live**, **stop** with the red button, and **File → Save As**, because live packets sit in a **temporary buffer that disappears** if you don't save. The default file format is **pcapng**, and you can export to legacy formats for other analyzers. The most useful technique in the lesson is the **ring buffer** in Capture Options: write a series of fixed-size files and overwrite the oldest. That's how you catch **intermittent problems** without filling the disk or ending up with one giant haystack PCAP.

---

## Core Concepts

### The welcome screen: picking an interface
Wireshark lists every interface its capture driver can see. Each has a **sparkline** (small traffic graph); **pick the one that's moving**, and hover over it for details.

| Interface type | Examples | Notes |
|---------------|----------|-------|
| Physical NICs | Wired USB/Ethernet adapter, Wi-Fi | What you'll use most |
| Loopback | `lo0` / Adapter for loopback traffic | Traffic between processes on the same host |
| Virtual / tunnel | `utun*` (VPN), `awdl0` (Apple AirDrop), `bridge*`, `docker0` | VPN-decrypted traffic lives on the tunnel interface |
| **Extcap** (plugins) | Cisco Remote Capture, **SSH Remote Capture** (sshdump), **UDP Listener** (udpdump), randpkt | Capture *from somewhere else* (a remote Linux box, a Cisco device) into your local Wireshark |

**On your Mac** (`dumpcap -D`): `en0 (Wi-Fi)`, `lo0 (Loopback)`, `utun0–7` (VPN/system tunnels), `awdl0`, `llw0`, `ap1`, `anpi*`. Your user is in the **`access_bpf`** group (`/dev/bpf*` is `root:access_bpf`), so **capture works without sudo**. That permission comes from the **ChmodBPF** helper installed with Wireshark.

### Live capture → stop → save
1. **Double-click** the active interface and packets scroll in live.
2. Status bar (bottom right): **Packets** (captured) vs **Displayed** (after any display filter).
3. **Red square** = stop. (Green fin = start again; it asks whether to discard the unsaved packets.)
4. **The capture is not saved yet.** Wireshark writes to a **temporary file**. Close without saving and the packets are **gone**.
5. **File → Save As** → name it → choose a format.

### File formats
| Format | When |
|--------|------|
| **pcapng** (*PCAP Next Generation*, default) | Normal use. Supports **multiple interfaces per file, per-packet comments, name resolution blocks, embedded TLS keys**, and nanosecond timestamps |
| **pcap** (libpcap) | Compatibility with older tools (some IDS, legacy scripts, tcpreplay setups) |
| Others (Microsoft NetMon, Novell LANalyzer, Sniffer, …) | Legacy analyzers |

> **Caption note:** The instructor first says "Packet Capture *Networks*," then corrects himself: **pcapng = PCAP Next Generation.**

### Capture Options (the ⚙️ icon, 3rd on the toolbar)
- **Input** tab: choose one or **several** interfaces, set a **capture filter** (BPF), snaplen, promiscuous mode.
- **Output** tab: **capture to a permanent file** (instead of the temp file), output format, and **automatic file rotation**.
- **Options** tab: name resolution, auto-scroll, **stop conditions** (after N packets, N files, size, or time).

### Ring buffer (multiple files)
```
Output → File: test.pcapng
☑ Create a new file automatically after… 500 megabytes
☑ Use a ring buffer with 20 files

test_00001_<timestamp>.pcapng  500 MB
test_00002_<timestamp>.pcapng  500 MB
…
test_00020_<timestamp>.pcapng  500 MB
test_00021_…  → oldest file (00001) deleted, so at most 20 files exist
```
- **Disk use is capped** at files × size = 20 × 500 MB = **10 GB**.
- Rotation criteria: **size**, **time** (e.g., every 10 min), or **packet count**.
- **Why:** an *intermittent, can't-reproduce-on-demand* problem. Leave it running, and when someone says "the spinning wheel is back," stop and open **the file that was being written at that moment**. You get a small, relevant file instead of one huge one.
- Without rotation, a long capture runs until **you stop it, the disk fills, or the system crashes**.

> **Caption note:** The transcript says "how many of these **500-byte** files." He means the **500-megabyte** files he just configured.

### The same thing from the command line (dumpcap / tshark / tcpdump)
For long-running captures, **dumpcap** (Wireshark's capture engine, without the GUI) is lighter and more reliable than leaving the GUI open:
```bash
export PATH="/Applications/Wireshark.app/Contents/MacOS:$PATH"
dumpcap -D                                    # list interfaces
dumpcap -i en0 -w ~/caps/test.pcapng \
        -b filesize:500000 -b files:20        # 20 × ~500 MB ring buffer (filesize is in kB)
dumpcap -i en0 -w ~/caps/t.pcapng -b duration:600 -b files:144   # 10-min files, 24 h kept
tshark  -i en0 -c 50                          # quick look: 50 packets to the terminal
# Linux / k3s node equivalent:
sudo tcpdump -i eth0 -s 0 -w /var/tmp/cap.pcap -C 500 -W 20   # -C in millions of bytes, -W = ring size
```

---

## Key Terms

| Term | Definition |
|------|-----------|
| Interface sparkline | Mini traffic graph per interface on the welcome screen; pick the active one |
| Capture driver | libpcap / BPF on macOS and Linux, Npcap on Windows. Lets Wireshark read packets |
| ChmodBPF / access_bpf | macOS helper and group granting non-root capture permission |
| Extcap | Wireshark plugin interfaces that capture remotely (SSH, Cisco, UDP listener) |
| Captured vs displayed | Total packets in the buffer vs those matching the current display filter |
| Temporary capture file | Where live packets go until you Save As; deleted on close |
| pcapng | PCAP Next Generation, the default format (multi-interface, comments, embedded keys) |
| pcap | Classic libpcap format for compatibility |
| Capture Options | Dialog for interfaces, capture filters, output files, stop conditions |
| Ring buffer | Rotating set of N capture files, overwriting the oldest, so disk use is capped |
| dumpcap | Wireshark's command-line capture engine; best for long captures |

---

## Real-World Connections

**Network+ links:** Where you capture matters. A **SPAN/port mirror or TAP** gets traffic that isn't addressed to your laptop ([[L14 - Network Monitoring]], [[L25 - Troubleshooting Tools]]). Ring buffers are the practical answer to the troubleshooting step "**duplicate the problem**" when it won't reproduce on demand ([[L21 - Network Troubleshooting Methodology]]).

**k3s lab:**
- Run a ring buffer on a node to catch intermittent Elasticsearch timeouts:
  `sudo tcpdump -i any -s 0 -w /var/tmp/es.pcap -C 200 -W 10 'tcp port 9200'`. Pull the newest file when Kibana complains.
- **SSH Remote Capture (extcap)**: in Wireshark, choose *SSH remote capture*, point it at your Ubuntu node, and set the remote command to `tcpdump -i cni0 -U -w -`. Packets stream **live into your Mac's Wireshark**. Pod traffic, no file copying.
- Kubeshark is effectively a **cluster-wide ring buffer**: it keeps a rolling window of traffic per node.

**Cloud:** **VPC Traffic Mirroring** (AWS) and **Packet Mirroring** (GCP) send copies to a collector VM, which then runs dumpcap with a ring buffer. That's the cloud version of SPAN plus long-term capture.

**Security / SOC:** Full-packet-capture appliances (Arkime/Moloch, Security Onion) are ring buffers at scale, with **retention set by disk size**.

---

## Troubleshooting Scenario

**Scenario:** Users report the VPN-based ERP app freezes "a few times a day," never on demand. A technician ran a single Wireshark capture overnight. By morning the laptop disk was full, Wireshark had crashed, and the 60 GB file wouldn't open.

**Analysis ([[L21 - Network Troubleshooting Methodology]]):** The method was wrong, not the idea. An open-ended GUI capture has no size limit, and a giant file is a haystack.

**Fix:**
```bash
dumpcap -i utun3 -f "host 10.20.0.15" -w ~/caps/erp.pcapng -b filesize:200000 -b files:50
```
- A capture filter limits traffic to the ERP server and keeps the files small.
- 50 × 200 MB = a **10 GB cap**.
- Capture on the **VPN tunnel interface** (`utun*`) to see decrypted inner traffic. On `en0` you'd only see encrypted ESP/TLS.

Ask users to note the **exact time** of each freeze, then open the file whose timestamp range covers it (`capinfos -a -e file`).

---

## Hands-On Ideas
1. Capture 30 s on `en0` while loading a website. Stop, **Save As** `first.pcapng`, then reopen it. Check *Statistics → Capture File Properties*.
2. Save the same capture as **pcap** and compare: `capinfos -t first.pcapng first.pcap` (file type, encapsulation).
3. Build a small ring buffer to watch rotation: `dumpcap -i en0 -w /tmp/r.pcapng -b filesize:1000 -b files:3`. Stream a video, then `ls -la /tmp/r_*`. Only 3 files ever exist.
4. Try a **time-based** ring: `-b duration:60 -b files:5` gives the last 5 minutes, always.
5. Set up **SSH remote capture** to your k3s node from the Wireshark welcome screen.

---

## Quiz

1. On the welcome screen, how do you pick the right interface?
   - A) The first one in the list  B) Always Loopback  C) The one whose sparkline shows traffic  D) Always an extcap interface

2. You captured for 5 minutes and closed Wireshark without saving. What happened to the packets?
   - A) Lost: they were in a temporary file  B) Auto-saved to Documents  C) Saved as pcap  D) Sent to the cloud

3. What is Wireshark's default save format, and what does it stand for?
   - A) pcap, Packet Capture  B) pcapng, PCAP Next Generation  C) cap, Capture  D) etl, Event Trace Log

4. Why might you save as classic pcap?
   - A) It's smaller  B) Compatibility with tools that don't support pcapng  C) It supports packet comments  D) It encrypts the file

5. A ring buffer is set to 500 MB files × 20 files. What is the maximum disk space used?
   - A) 500 MB  B) 20 GB  C) Unlimited  D) 10 GB

6. What problem is a ring buffer best for?
   - A) Catching intermittent issues that can't be reproduced on demand  B) Measuring cable length  C) Decrypting TLS  D) Assigning IP addresses

7. Which interfaces let you capture on a remote machine from your local Wireshark?
   - A) Loopback  B) Extcap interfaces such as SSH Remote Capture  C) awdl0  D) Bluetooth

8. You need to see application traffic inside a VPN on macOS. Which interface should you capture on?
   - A) en0  B) lo0  C) The VPN tunnel interface (utunX)  D) awdl0

**Answers:** 1-C, 2-A, 3-B, 4-B, 5-D, 6-A, 7-B, 8-C

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[WS01 - Pre-Lesson - Your First Lab]] — Analyzing a capture (previous lesson)
- [[wireshark]] — Wireshark reference (capture section)
- [[wireshark-101]] — Course progress
- [[L14 - Network Monitoring]] — SPAN, TAP, packet capture
- [[L25 - Troubleshooting Tools]] — tcpdump/tshark, protocol analyzers
- [[L21 - Network Troubleshooting Methodology]] — Duplicating intermittent problems
