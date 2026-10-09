# Networking & Cloud Wiki — Schema

## Purpose
This wiki supports a structured self-study program in **Networking, Cloud Computing, and Infrastructure Engineering**. The LLM acts as a personal coach/mentor; the learner provides transcripts and directs sessions.

## Directory Structure
```
Networking + Cloud/
├── CLAUDE.md                        ← this schema file
├── raw/                             ← immutable source documents (never edit)
│   ├── course_tracker.md            ← L01–Lxx lesson tracking data
│   ├── transcripts/                 ← raw course transcript files (.txt)
│   └── assets/                      ← downloaded PDFs, slides, attachments
└── wiki/                            ← LLM-maintained knowledge base
    ├── index.md                     ← full catalog of all wiki pages
    ├── log.md                       ← append-only activity log
    ├── overview.md                  ← learning goals, roadmap, current status
    ├── lessons/                     ← lessons organized by course
    │   ├── network-plus/            ← "Lxx - Title.md" (CompTIA Network+)
    │   └── gcp/                     ← "GCPxx - Title.md" (Google Cloud Networking)
    ├── concepts/                    ← technology and concept reference pages
    ├── certs/                       ← per-certification tracking and progress
    └── labs/                        ← hands-on projects and exercises
```

## Page Formats

### Lesson pages (`wiki/lessons/<track>/Lxx - Title.md`)
File name = `<ID> - <Title>.md` (ID first so files sort in course order; `:` and `/` in titles become ` -`). Frontmatter keeps `id` plus `aliases: [<ID>]`. Link with the full file name: `[[L09 - Routing Technologies]]`.
```yaml
---
id: L01
title: Full Lesson Title
course: CompTIA Network+ | AWS Cloud Practitioner | AWS Solutions Architect | GCP Networking
cert_track: network-plus | aws-ccp | aws-saa | gcp-networking
objective: "1.2 — Compare and Contrast Networking Appliances"
date: YYYY-MM-DD
status: Complete | In Progress | Needs Review
quiz_attempted: Yes | No
weak_areas: []
tags: [osi, routing, vpc, subnetting]
---
```
Sections: **Summary**, **Core Concepts**, **Key Terms**, **Real-World Connections**, **Troubleshooting Scenario**, **Hands-On Ideas**, **Quiz**, **Weak Areas to Revisit**

### Concept pages (`wiki/concepts/xxx.md`)
Definition, role in networking/cloud, key properties, tools, CLI commands, AWS/GCP equivalents, and linked lessons that cover this concept.

### Cert pages (`wiki/certs/xxx.md`)
Exam objectives, lessons completed per objective, weak areas, practice question notes, target exam date.

### Lab pages (`wiki/labs/xxx.md`)
Lab goal, topology/setup, commands used, observations, concepts demonstrated, and links to relevant lesson/concept pages.

## Workflows

### Ingest a new lesson
1. Drop transcript into `raw/transcripts/` named `Lxx_Topic.txt`
2. Say: `ingest L04 — [lesson title]`
3. LLM reads → creates `wiki/lessons/network-plus/L04 - <Title>.md` → updates `wiki/index.md` → updates relevant concept pages → updates cert tracking page → appends to `wiki/log.md`

### Query
Ask anything. LLM reads `index.md` first, then relevant pages. Good synthesis gets filed as a new concept or lab page.

### Quiz me
Say: "quiz me on [topic]" — LLM generates scenario-based questions, tracks weak areas in the lesson page.

### Lint
Say: "lint the wiki". LLM checks: contradictions between pages, orphan pages, missing concept links, stale weak-area flags, and suggests next study topics.

## Learner Profile

**Name:** Tah

**Background:**
- k3s / Kubernetes — running observability stack (Kubeshark, Elasticsearch, Kibana)
- Linux — ARM64 Ubuntu 22.04, CLI-fluent
- Telecom coursework — V2X / 5G NR sidelink work
- Full-stack — Next.js + PostgreSQL projects
- Networking — conceptual foundation from coursework

**Calibration:** Skip basic IP address definitions. Go deep on architecture, why things work, and production patterns. Always connect concepts to the k3s lab, AWS VPC, or enterprise infrastructure.

## Certification Tracks

| Cert | Track ID | Status | Target |
|------|----------|--------|--------|
| CompTIA Network+ N10-009 | `network-plus` | In Progress | TBD |
| AWS Cloud Practitioner | `aws-ccp` | Not Started | TBD |
| AWS Solutions Architect – Associate | `aws-saa` | Not Started | TBD |
| Google Cloud Networking Specialization | `gcp-networking` | Not Started | TBD |
| Kubernetes / Docker / Linux / DevOps | `k8s-devops` | Ongoing (lab) | TBD |

## Teaching Structure (per session)
Every lesson response follows:
1. Lesson Summary
2. Core Concepts
3. Important Terms
4. Real-World Example
5. Troubleshooting Scenario
6. Hands-On Practice Ideas
7. Quiz Questions
8. Quick Recap

## Log Format
Each entry: `## [YYYY-MM-DD] action | description`
