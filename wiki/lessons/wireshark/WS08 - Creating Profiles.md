---
id: WS08
aliases: [WS08]
title: Creating Profiles
course: Wireshark 101 - Packet Analysis Essentials
cert_track: wireshark
module: Module 2 - The Wireshark Interface
date: 2026-10-10
status: Complete
quiz_attempted: No
weak_areas: []
lab_file: raw/assets/wireshark-101/Pre-Lab-SlowNetwork.pcapng
tags: [wireshark, profiles, configuration-profiles, coloring-rules-copy-from, frame-metadata, delta-time, filter-buttons, workflow]
---

# WS08 — Creating Profiles

## Summary
A **profile** is a **saved collection of all your Wireshark customizations**: columns, coloring rules, **filter buttons**, layout, font size, preferences. You switch between profiles with one click, depending on the job: **day-to-day troubleshooting, deep TCP, DNS, cybersecurity**. Everything done so far (Delta column, SYN coloring rule) went into the **Default** profile. This lesson creates a new **"Wireshark 101"** profile used for the rest of the course, rebuilds the customizations in it (**Coloring Rules → Copy from → Default**; Delta column again), and shows a faster way to add Delta: drag **"Time delta from previous displayed frame"** from the **Frame** section of Packet Details.

---

## Core Concepts

### What a profile contains
| Setting | Example |
|---------|---------|
| **Columns** | Delta, TTL, `tcp.stream`… ([[WS06 - Configuring Columns]]) |
| **Coloring rules** | TCP SYNs green ([[WS07 - Coloring Traffic]]) |
| **Filter buttons** | One-click display filters on the toolbar (coming next in the course) |
| Layout & fonts | Pane arrangement, font size (zoom with View → Zoom In/Out, ⌘+/⌘−) |
| Preferences | Name resolution, protocol settings, TCP analysis options, recent files… |
| Decode As, IO graph settings, display/capture filter bookmarks | Per-profile too |

**Every change is saved to whichever profile is active.** Check the status bar (bottom right) before customizing ([[WS05 - The Default Interface]]).

### Create a profile (the lesson's method)
1. **Right-click the Profile area** (status bar, bottom right) → **New…**
2. Name it **`Wireshark 101`** → OK. The status bar now shows *Profile: Wireshark 101*.
3. Note that the **new profile starts with stock settings**: the Delta column and the SYN coloring rule from Default **aren't there**. Profiles are independent.

Full management: **Edit → Configuration Profiles** (macOS: also under the Wireshark menu / status bar), with **New, Copy, Delete, Import (zip/directory), Export**.

> **Tip (beyond the lecture):** to start from an existing profile instead of stock, select it in *Configuration Profiles* and click **Copy**. You inherit all its settings.

### Rebuild the customizations in the new profile
**Coloring rules: Copy from**
**View → Coloring Rules → Copy from (bottom of the dialog) → Default.** This adds Default's rules (including your green **TCP SYNs**) into the current profile's list. Then **remove the duplicates** so each rule appears once. The instructor deletes the top entries that came in twice.

**Delta column, two ways:**
| Way | Steps |
|-----|-------|
| Preferences | Edit → Preferences (macOS: **Wireshark → Settings**) → Appearance → Columns → + → *Delta time displayed* → drag next to Time |
| **Frame section (faster)** | Packet Details → expand **Frame N** → drag **"Time delta from previous displayed frame"** (`frame.time_delta_displayed`) to the column header |

### The Frame section is metadata, not protocol
At the top of Packet Details, **Frame N: …** is **not a header on the wire**. It's information Wireshark records **about** the frame:
| Field | Filter name | Meaning |
|-------|-------------|---------|
| Arrival Time | `frame.time` | Wall-clock capture time |
| Epoch Time | `frame.time_epoch` | Seconds since **1970-01-01 00:00:00 UTC** (Unix epoch) |
| Time delta from previous captured frame | `frame.time_delta` | Gap to the previous packet in the file |
| Time delta from previous displayed frame | `frame.time_delta_displayed` | Gap to the previous **visible** packet |
| Time since reference or first frame | `frame.time_relative` | The stopwatch value |
| Frame Number / Length / Capture Length | `frame.number`, `frame.len`, `frame.cap_len` | `cap_len < len` means the packet was sliced (Pre-Lab: 96-byte snaplen, [[WS01 - Pre-Lesson - Your First Lab]]) |
| Coloring Rule Name | `frame.coloring_rule.name` | Which rule colored it ([[WS07 - Coloring Traffic]]) |
| Protocols in frame | `frame.protocols` | e.g. `eth:ethertype:ip:tcp:http` |

### Which profiles to keep
The instructor has many profiles but **uses one or two most of the time**:
| Profile | Purpose | Contents |
|---------|---------|----------|
| **TCP Plain** (his main working profile) | Everyday troubleshooting | **Predefined filter buttons**, select coloring rules, layout, a few columns |
| **TCP Advanced** | Deep TCP analysis only | Seq, next seq, ack, **bytes in flight**: too busy for daily use |
| **DNS** | Name-resolution issues (mostly UDP, so TCP columns are useless) | DNS-specific columns (built later in the course) |
| **Cybersec** | Forensics / threat hunting | Threat-hunting coloring rules ([[WS07 - Coloring Traffic]]) |
| **Wireshark 101** | This course | Built step by step from here on |

---

## Profile Tips (beyond the lecture)
| Tip | How |
|-----|-----|
| **Where profiles live** | `~/.config/wireshark/profiles/<Name>/` on macOS/Linux (`%APPDATA%\Wireshark\profiles\` on Windows). Each is a folder of plain-text files: `preferences`, `colorfilters`, `dfilter_buttons`, `recent`… |
| **Back up / share** | Edit → Configuration Profiles → **Export** → zip. Teammates **Import** the same zip, which is how the course profiles were shipped |
| **Auto-switch** | In Configuration Profiles, set an **auto switch filter** (e.g., `dns` → DNS profile, `tcp.port == 9200` → k3s profile). Wireshark switches when an opened file matches |
| **Use a profile from the CLI** | `tshark -C "Wireshark 101" -r file.pcapng` applies that profile's preferences (columns via `-T fields` still need `-e`) |
| **Keep Default clean** | Experiment in a copy so you can always compare with stock behavior |

---

## Key Terms

| Term | Definition |
|------|-----------|
| Configuration profile | Named folder of Wireshark settings (columns, colors, buttons, layout, prefs) |
| Default profile | Built-in profile active until another is selected |
| Copy from (coloring rules) | Imports another profile's coloring rules into the current profile |
| Filter button | Toolbar button that applies a saved display filter with one click |
| Frame section | Wireshark-generated metadata about each captured frame (not on the wire) |
| `frame.time_delta_displayed` | Time since the previous displayed frame (the Delta column field) |
| Epoch time | Seconds since 1970-01-01 00:00:00 UTC |
| Bytes in flight | Data sent but not yet acknowledged (TCP Advanced profile column) |
| Auto switch filter | Display filter that makes Wireshark switch to a profile automatically |

---

## Real-World Connections
- **Network+:** One profile per task mirrors troubleshooting **by layer and protocol** ([[L21 - Network Troubleshooting Methodology]]): DNS problems need DNS fields ([[L16 - IP Services]]), TCP performance needs window and in-flight fields ([[L24 - Troubleshooting Network Performance Issues]]).
- **Team/SOC practice:** Export the team's standard profiles to a shared repo, so every analyst opens a PCAP with the same columns and colors and ticket notes like "the red rows" mean the same thing to everyone.
- **This wiki:** Version the profile folder itself. Copy `~/.config/wireshark/profiles/Wireshark 101/` into `raw/assets/` occasionally, or export a zip, so your setup is backed up with your notes.
- **k3s lab:** A `Tah-k3s` profile with an auto-switch filter `tcp.port == 9200 || tcp.port == 6443 || udp.port == 8472` loads Elasticsearch/API-server/Flannel columns and colors whenever you open a cluster capture.

---

## Troubleshooting Scenario
**Scenario:** After creating the "Wireshark 101" profile, a learner says, "Wireshark lost my Delta column and my green SYN rule."
**Analysis:** Nothing was lost. They're **in the Default profile**, and the new profile started with stock settings. Profiles are separate folders.
**Fix:** In *Wireshark 101*: **View → Coloring Rules → Copy from → Default** (delete duplicates), then add Delta again by dragging *Frame → Time delta from previous displayed frame* to the header. In future, create a profile with **Copy** in *Configuration Profiles* to inherit settings.

---

## Hands-On Ideas
1. Create **Wireshark 101** (status bar → right-click → New). Rebuild the SYN rule with **Copy from Default** and remove the duplicates.
2. Re-add Delta by **dragging from the Frame section**, then add `frame.cap_len` next to Length to see the 96-byte slicing in the Pre-Lab file.
3. Import both instructor profiles (`raw/assets/wireshark-101/Profiles/*.zip`) and flip between **TCP Plain**, **Cybersec** and **Wireshark 101** on the same file. Notice the different columns, colors and buttons.
4. Find the profile folder: `ls ~/.config/wireshark/profiles/` and `cat ~/.config/wireshark/profiles/"Wireshark 101"/colorfilters`.
5. Set an **auto switch filter** `dns` on a future DNS profile, and test it with any capture containing DNS.

---

## Quiz

1. What is a Wireshark profile?
   - A) A saved capture file  B) A user account  C) A collection of settings such as columns, coloring rules, filter buttons, and layout  D) A network interface

2. How does the lesson create a new profile?
   - A) Right-click the Profile area in the status bar → New  B) File → New  C) Capture → Options  D) Statistics → Profiles

3. After switching to a new profile, your custom coloring rule is gone. Why?
   - A) It was deleted  B) Each profile has its own settings; the rule lives in Default  C) Coloring was disabled globally  D) The capture file changed

4. Which button brings another profile's coloring rules into the current one?
   - A) Import  B) Merge  C) Reset  D) Copy from

5. Which Packet Details section lets you drag "Time delta from previous displayed frame" to create a Delta column?
   - A) Ethernet II  B) Internet Protocol  C) Frame  D) TCP

6. Is the Frame section part of the packet on the wire?
   - A) Yes, it's the Ethernet header  B) No, it's metadata Wireshark records about the captured frame  C) Yes, it's the IP header  D) Only on Wi-Fi

7. Why keep separate profiles (e.g., TCP Plain vs TCP Advanced vs DNS)?
   - A) Different tasks need different columns, colors, and buttons, and one profile for everything gets cluttered  B) Wireshark requires it  C) To save disk space  D) Profiles speed up captures

8. Epoch time counts seconds since when?
   - A) The first packet  B) 2000-01-01  C) 1970-01-01 00:00:00 UTC  D) Wireshark's install time

**Answers:** 1-C, 2-A, 3-B, 4-D, 5-C, 6-B, 7-A, 8-C

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[WS05 - The Default Interface]] — Profile indicator in the status bar
- [[WS06 - Configuring Columns]] — Delta column
- [[WS07 - Coloring Traffic]] — SYN coloring rule, instructor's profiles
- [[WS00 - Introduction]] — Course profile files
- [[wireshark]] — Wireshark reference · [[wireshark-101]] — Course progress
