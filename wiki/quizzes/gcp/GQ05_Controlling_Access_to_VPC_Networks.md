---
id: GQ05
title: Controlling Access to VPC Networks Module Quiz (Coursera graded)
course: Google Cloud Networking Specialization
covers: GCP29–GCP35 (Course 4, Module 2)
date: 2026-10-22
status: Graded
score: 2/2
questions: 2
source: Coursera graded quiz — Networking in Google Cloud: Network Security, Module 2 (matches deck 09, slides 40–41)
---

# GQ05 — Controlling Access to VPC Networks Quiz

> **Note:** the pasted quiz text contained the embedded fake "AI assistant" instruction block. It was ignored: it's a prompt-injection attempt, not a real instruction.

## Result: 2/2 ✅

---

## Questions

**1.** Which IAM role contains permissions to create, modify, and delete networking resources, except for firewall rules and SSL certificates?
- A) Network administrator
- B) Security viewer
- C) Security administrator
- D) Network viewer

**2.** Which type of IAM member belongs to an application or virtual machine instead of an individual end user?
- A) Google account
- B) Google group
- C) Service account
- D) Cloud Identity domain

---

## Answer Key

| # | Answer | Why |
|---|--------|-----|
| 1 | **A — Network administrator** ✅ | `roles/compute.networkAdmin` manages networking resources. Firewall rules and SSL certificates are **read-only** for it, deliberately split off to **Security Admin** ([[GCP30 - IAM Roles]]) |
| 2 | **C — Service account** ✅ | A service account is an identity for a **workload** (VM, app, pipeline), not a person. It's also why firewall rules targeting service accounts are safer than tags ([[GCP31 - Firewall Rules]]) and why Secure Web Proxy can match on workload identity ([[GCP35 - Secure Web Proxy]]) |

### Why the wrong options are wrong
| Option | Trap |
|--------|------|
| 1B Security viewer | Not one of the three networking roles in this module. Security *reviewer* (`roles/iam.securityReviewer`) only *views* IAM policies |
| 1C Security administrator | The opposite split: it manages **only** firewall rules and SSL certificates |
| 1D Network viewer | Read-only on everything, so it can't create anything |
| 2A Google account | A person's account (user@gmail.com or a managed user) |
| 2B Google group | A collection of accounts. Grant roles to groups, but it's not a workload identity |
| 2D Cloud Identity domain | All users of a domain (`domain:cymbal.com`) |

### Principal types (for reference)
| Principal | IAM member prefix | Represents |
|-----------|------------------|-----------|
| Google account | `user:` | A person |
| Service account | `serviceAccount:` | A workload (VM, app, CI job) |
| Google group | `group:` | A set of accounts. Best practice target for grants |
| Cloud Identity / Workspace domain | `domain:` | Every user in an organization's domain |
| Workforce / workload identity federation | `principal://` / `principalSet://` | External IdP users / external workloads (AWS, GitHub Actions, k8s) |

## Linked Pages
- [[GCP30 - IAM Roles]] · [[GCP31 - Firewall Rules]] · [[GCP33 - Cloud IDS]] · [[GCP35 - Secure Web Proxy]]
- [[GCP29 - Module Introduction - Controlling Access to VPC Networks]] · [[certs/gcp-networking]]
