---
course: Wireshark 101 - Packet Analysis Essentials
track: wireshark
status: In Progress
started: 2026-10-10
lab_files: raw/assets/wireshark-101/
---

# Wireshark 101: Packet Analysis Essentials

Lab-heavy course on protocol analysis for troubleshooting and security investigation. It's not a certification, but it backs up Network+ objective 5.5, CKS runtime security, and SOC work. Runs in parallel with the *Networking in Google Cloud* program.

## Lab Files (`raw/assets/wireshark-101/`)
| File | Used in |
|------|---------|
| Pre-Lab-SlowNetwork.pcapng | [[WS01 - Pre-Lesson - Your First Lab]], [[WS05 - The Default Interface]], [[WS06 - Configuring Columns]] |
| Lab 1-IntrotoWireshark.pcapng | — |
| Lab 2-DisplayFilters.pcapng | — |
| Lab 3-TCP Retrans.pcapng | — |
| Lab 4-NetworkCongestion.pcapng | — |
| Lab 5-AnotherSlowApp.pcapng | — |
| Lab 6-TCPResets.pcapng | — |
| Lab 7-TCPIssues.pcapng | — |
| Profiles/Chris-TCPPlain.zip, Chris-Security.zip | Instructor's Wireshark configuration profiles (import via Edit → Configuration Profiles) |

## Lessons

### Module 1 - Introduction to Wireshark
| ID | Title | Status |
|----|-------|--------|
| [[WS00 - Introduction]] | Introduction (why packet analysis, course files, profiles) | ✅ Complete |
| [[WS01 - Pre-Lesson - Your First Lab]] | Pre-Lesson: Your First Lab (slow network: client vs network vs server) | ✅ Complete |
| [[WS02 - Your First Packet Capture]] | Your First Packet Capture (interfaces, save, ring buffer) | ✅ Complete |
| [[WS03 - Where to Capture on the Network]] | Where to Capture (endpoint, SPAN, TAP, cloud) | ✅ Complete |
| [[WS04 - Packet Capture - Best Practices]] | Packet Capture Best Practices | ✅ Complete |
| [[WQ01_Introduction_to_Wireshark]] | Module quiz | ✅ Reviewed |

### Module 2 *(module title not yet confirmed)*
| ID | Title | Status |
|----|-------|--------|
| [[WS05 - The Default Interface]] | The Default Interface (panes, profile, coloring) | ✅ Complete |
| [[WS06 - Configuring Columns]] | Configuring Columns (Delta, UTC, TTL) | ✅ Complete |

## Quizzes
| ID | Title | Module | Status |
|----|-------|--------|--------|
| [[WQ01_Introduction_to_Wireshark]] | Introduction to Wireshark Quiz | Module 1 | ✅ Reviewed |

## Key Facts (collected from sessions)
| Topic | Key Fact |
|-------|---------|
| Client vs server | Client sends the first SYN; server listens on the well-known port |
| Network RTT | SYN → SYN/ACK gap in a client-side capture |
| Server think time | Server ACK of request → first response data |
| Course files | 7 numbered lab PCAPs + Pre-Lab + 2 instructor profiles (Packet Pioneer GitHub, Wireshark 101) |
| Spotting gaps | Time Display Format → Seconds Since Previous Displayed Packet |
| Unsaved capture | Lives in a temp file; lost on close unless you Save As |
| Ring buffer | N files × size (e.g., 20 × 500 MB = 10 GB cap) for intermittent problems |
| Capture points | Endpoint (quick, adds load) → SPAN (on-wire, can oversubscribe) → TAP (sees all, costs $) → cloud mirroring; server install = last resort, CLI only |
| SPAN oversubscription | 3 × 1G full-duplex = 6 Gbps into 1G monitor → drops → `Previous segment not captured` |
| Capture best practices | No capture filter first · client first, apps closed · ring buffer · confirm problem happened there · own a TAP · client + server simultaneously (NTP-synced) |
| Bundled CLI tools | tshark, dumpcap, capinfos, editcap, mergecap, rawshark, text2pcap, reordercap |
| Capture driver | Npcap (Windows) / libpcap-BPF (macOS, Linux): required for live capture only |
| Supported OS | Windows, macOS, Linux (no official iOS/Android) |
| Default layout (4.x) | Packet List top · Packet Details bottom-left · Packet Bytes bottom-right; profile shown bottom-right of status bar |
| Coloring rules | Top-down, first match wins; toggle with Colorize Packet List; Bad TCP = black/red, RST = dark red |
| Add a column | Preferences → Columns (+) · drag field to header · right-click → Apply as Column; macOS Preferences under Wireshark menu |
| Delta column | Delta time *displayed*, sort to find worst gaps — check Info (keep-alives ≠ problem) |
| Time of Day vs UTC | Time of Day = viewer's local TZ; use UTC when collaborating |
| TTL | Hops away = initial (64/128/255) − observed; unchanged initial TTL = captured at that host |
| Snaplen | "Packet size limited during capture" = sliced capture; timing works, payload doesn't |

## Weak Areas to Revisit
*(none flagged yet)*
