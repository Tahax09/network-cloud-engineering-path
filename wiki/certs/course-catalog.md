---
title: Course Catalog (Coursera via Recoded)
type: cert-catalog
updated: 2026-10-16
tags: [catalog, roadmap, coursera, cisco, ccna, aws, azure, kubernetes, cka, cks, kcna, docker, ai]
---

# Course Catalog

Every Coursera program on the study list, accessed through the **Recoded** organization. Each one shows the exam it prepares for and where it fits in the roadmap. Titles, providers and course counts were checked against the Coursera pages on 2026-10-16.

> **Links:** each title links to the Recoded org page you sent (sign-in required). A program becomes a full cert page in `wiki/certs/` once its first lesson is ingested.

---

## In progress (already in the wiki)
| Program | Provider | Exam | Status | Wiki |
|---------|----------|------|--------|------|
| CompTIA Network+ N10-009 | — | N10-009 | Course complete, exam prep | [network-plus](network-plus.md) |
| Networking in Google Cloud (6 courses) | Google Cloud | — (specialization) | Course 4 in progress | [gcp-networking](gcp-networking.md) |
| Wireshark 101: Packet Analysis Essentials | — | — | In progress | [wireshark-101](wireshark-101.md) |

---

## Cisco networking
| Program | Type | Provider | Courses | Prepares for | Track ID | Status |
|---------|------|----------|---------|--------------|----------|--------|
| [Network Fundamentals](https://www.coursera.org/organizations/recoded/specializations/network-fundamentals) | Specialization | Cisco Learning and Certifications | 4 | CCNA groundwork | `cisco-netfund` | Not Started |
| [Cisco CCNA (200-301)](https://www.coursera.org/organizations/recoded/specializations/packt-cisco-ccna-200-301) | Specialization | Packt | 3 | **CCNA 200-301** | `ccna` | Not Started |
| [Network Automation Engineering Fundamentals](https://www.coursera.org/organizations/recoded/specializations/networkautomation) | Specialization | Cisco Learning and Certifications | 5 | CCNA automation domain; groundwork for Cisco DevNet / CCNA Automation | `cisco-netauto` | Not Started |
| [Understanding Cisco Wireless Foundations](https://www.coursera.org/organizations/recoded/specializations/understanding-cisco-wireless-foundations) | Specialization | Cisco Learning and Certifications | 4 | CCNA wireless topics; groundwork for CCNP Wireless | `cisco-wireless` | Not Started |
| [Cisco Software-Defined WAN for Enterprise and Cloud](https://www.coursera.org/organizations/recoded/specializations/pearson-cisco-software-defined-wan-for-enterprise-and-cloud) | Specialization | Pearson | 5 | CCNP Enterprise SD-WAN concentration (300-415 ENSDWI) | `cisco-sdwan` | Not Started |

## Security operations
| Program | Type | Provider | Courses | Prepares for | Track ID | Status |
|---------|------|----------|---------|--------------|----------|--------|
| [Cybersecurity Operations Fundamentals](https://www.coursera.org/organizations/recoded/specializations/cbrops) | Specialization | Cisco Learning and Certifications | 7 | **Cisco CyberOps Associate (200-201 CBROPS)** | `cbrops` | Not Started |

## AWS
| Program | Type | Provider | Courses | Prepares for | Track ID | Status |
|---------|------|----------|---------|--------------|----------|--------|
| [AWS Cloud Practitioner Essentials](https://www.coursera.org/organizations/recoded/learn/aws-cloud-practitioner-essentials) | Course | Amazon Web Services | 1 | **AWS Certified Cloud Practitioner (CLF-C02)** | `aws-ccp` | Not Started ([aws-ccp](aws-ccp.md)) |
| [AWS Fundamentals](https://www.coursera.org/organizations/recoded/specializations/aws-fundamentals) | Specialization | Amazon Web Services | 3 | CCP / SAA groundwork | `aws-fund` | Not Started |
| [AWS Cloud Solutions Architect](https://www.coursera.org/organizations/recoded/professional-certificates/aws-cloud-solutions-architect) | Professional Certificate | Amazon Web Services | 4 | **AWS Solutions Architect – Associate (SAA-C03)** | `aws-saa` | Not Started ([aws-saa](aws-saa.md)) |

## Microsoft Azure
| Program | Type | Provider | Courses | Prepares for | Track ID | Status |
|---------|------|----------|---------|--------------|----------|--------|
| [AZ-900 Azure Fundamentals Crash Course](https://www.coursera.org/organizations/recoded/learn/packt-az-900-azure-fundamentals-crash-course-2o37z) | Course | Packt | 1 | **AZ-900** | `az-900` | Not Started |
| [AZ-104 Microsoft Azure Administrator Certification](https://www.coursera.org/organizations/recoded/learn/packt-az-104-microsoft-azure-administrator-certification) | Course | Packt | 1 | **AZ-104** | `az-104` | Not Started |
| [Microsoft Azure Administrator (AZ-104)](https://www.coursera.org/organizations/recoded/specializations/packt-mircrosoft-azure-administrator-az-104) | Specialization | Packt | 3 | **AZ-104** | `az-104` | Not Started |

> The two AZ-104 entries overlap: a single course and a 3-course specialization from the same publisher. Pick one as the main track and use the other for review.

## Kubernetes and containers
| Program | Type | Provider | Courses | Prepares for | Track ID | Status |
|---------|------|----------|---------|--------------|----------|--------|
| [Docker and Kubernetes Masterclass: From Beginner to Advanced](https://www.coursera.org/organizations/recoded/specializations/packt-docker-and-kubernetes-masterclass-from-beginner-to-advanced) | Specialization | Packt | 4 | Hands-on foundation for KCNA/CKA | `docker-k8s` | Not Started |
| [Kubernetes and Cloud Native Associate](https://www.coursera.org/organizations/recoded/learn/pearson-kubernetes-and-cloud-native-associate-kcna-complete-course) | Course | Pearson | 1 | **KCNA** | `kcna` | Not Started |
| [Certified Kubernetes Administrator (CKA)](https://www.coursera.org/organizations/recoded/specializations/certified-kubernetes-administrator) | Specialization | Pearson | 6 | **CKA** | `cka` | Not Started |
| [Certified Kubernetes Security Specialist (CKS)](https://www.coursera.org/organizations/recoded/specializations/pearson-certified-kubernetes-security-specialist-cks) | Specialization | Pearson | 8 | **CKS** (needs an active CKA) | `cks` | Not Started |

## AI
| Program | Type | Provider | Courses | Prepares for | Track ID | Status |
|---------|------|----------|---------|--------------|----------|--------|
| [Google AI Professional Certificate](https://www.coursera.org/organizations/recoded/professional-certificates/google-ai) | Professional Certificate | Google | 8 | Coursera / Google certificate (no separate exam) | `google-ai` | Not Started |

---

## Where they fit in the roadmap
```
Now       Network+ (exam prep) · Wireshark 101 · Networking in Google Cloud (Course 4)
Next      Docker & Kubernetes Masterclass ─▶ KCNA ─▶ CKA ─▶ CKS
Cloud     AZ-900 ─▶ AZ-104 ─▶ AZ-500          AWS Cloud Practitioner Essentials / AWS Fundamentals ─▶ CCP ─▶ AWS Solutions Architect (SAA)
Cisco     Network Fundamentals ─▶ CCNA 200-301 ─▶ Wireless Foundations · Network Automation · SD-WAN
Security  Cybersecurity Operations Fundamentals (CyberOps 200-201)
Anytime   Google AI Professional Certificate
```
- **Kubernetes** follows the order you already chose (KCNA, then CKA). The Masterclass goes first as hands-on practice, which fits your k3s lab.
- **Azure:** AZ-104 is the usual step before AZ-500 (it's no longer a formal prerequisite, but most AZ-500 content assumes it). AZ-900 is optional if you go straight to AZ-104.
- **Cisco:** Network+ already covers most of Network Fundamentals, so expect to move through it fast. CCNA builds directly on your Network+ lessons (L01–L25).
- **CKS** requires an active CKA before you can sit the exam.

## Linked Pages
- [[overview]] · [[network-plus]] · [[gcp-networking]] · [[wireshark-101]] · [[aws-ccp]] · [[aws-saa]]
