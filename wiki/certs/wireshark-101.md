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
| Pre-Lab-SlowNetwork.pcapng | [[WS01 - Pre-Lesson - Your First Lab]] |
| [[WS02 - Your First Packet Capture]] | Your First Packet Capture (interfaces, save, ring buffer) | ✅ Complete |
| Lab 1-IntrotoWireshark.pcapng | — |
| Lab 2-DisplayFilters.pcapng | — |
| Lab 3-TCP Retrans.pcapng | — |
| Lab 4-NetworkCongestion.pcapng | — |
| Lab 5-AnotherSlowApp.pcapng | — |
| Lab 6-TCPResets.pcapng | — |
| Lab 7-TCPIssues.pcapng | — |
| Profiles/Chris-TCPPlain.zip, Chris-Security.zip | Instructor's Wireshark configuration profiles (import via Edit → Configuration Profiles) |

## Lessons
| ID | Title | Status |
|----|-------|--------|
| [[WS00 - Introduction]] | Introduction (why packet analysis, course files, profiles) | ✅ Complete |
| [[WS01 - Pre-Lesson - Your First Lab]] | Pre-Lesson: Your First Lab (slow network: client vs network vs server) | ✅ Complete |

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
| Snaplen | "Packet size limited during capture" = sliced capture; timing works, payload doesn't |

## Weak Areas to Revisit
*(none flagged yet)*
