---
id: GCP23
aliases: [GCP23]
title: Network Security - Course Introduction
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 4: Network Security — Introduction"
date: 2026-10-15
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, network-security, course-intro, ddos, cloud-armor, access-control, firewall, iam, security-monitoring, cloud-ids, flow-logs]
---

# GCP23 — Network Security: Course Introduction

## Summary
This opens **Course 4 of 6** in *Networking in Google Cloud*: **Network Security**, covering the services that safeguard your Google Cloud network. There are three modules:

| Module | Goal |
|--------|------|
| **1. Distributed Denial-of-Service (DDoS) Protection** | Fortify the network against **DDoS** to keep services **available** |
| **2. Controlling Access to VPC Networks** | **Network access control**: define **who** can reach your resources and **how** |
| **3. Advanced Security Monitoring and Analysis** | **Proactively detect and respond** to threats |

---

## Core Concepts

### Where this course sits
```
Course 1  Fundamentals            ✅ (not yet in wiki)
Course 2  Routing and Addressing  ✅ GCP01–GCP13
Course 3  Network Architecture    ✅ GCP14–GCP22 (+ GQ03; GQ02 pending)
Course 4  Network Security        ◀ you are here (GCP23 →)
Course 5  Load Balancing
Course 6  Hybrid and Multicloud
```

### Likely services per module (preview, confirmed as lessons arrive)
| Module | Probable Google Cloud services | CIA pillar |
|--------|-------------------------------|-----------|
| DDoS protection | **Google Cloud Armor** (WAF, rate limiting, Adaptive Protection), global load balancers absorbing L3/L4 attacks at Google's edge | **Availability** |
| Access control | **VPC firewall rules / firewall policies** (hierarchical, network, regional), **IAM** for network admins, **IAP**, **VPC Service Controls**, Cloud NGFW | **Confidentiality / integrity** |
| Monitoring & analysis | **Cloud IDS**, **Packet Mirroring**, **VPC Flow Logs**, **Firewall Rules Logging / Firewall Insights**, Cloud Logging, Security Command Center | **Detect & respond** |

### Already built in earlier lessons
| Security idea | Seen in |
|---------------|---------|
| No external IPs + IAP-only SSH (`35.235.240.0/20`) | [[GCP12 - Lab - Implement Private Google Access and Cloud NAT]] |
| Service-account firewall rules, tier isolation | [[GCP15 - Cloud Network Architecture Overview]], [[GS03 - Architecture and Topology Scenarios]] |
| Gated egress/ingress, Cloud NGFW Enterprise IPS | [[GCP20 - Other Topologies - Mesh, Mirrored, Gated]] |
| Multilayer security best practices (Cloud Armor, Cloud IDS, NGFW IPS, Threat Intelligence) | [[GCP22 - Best Practices for Connectivity and Topologies]] |
| VPC Service Controls via `restricted.googleapis.com` | [[GCP08 - Private Google Access]] |

---

## Key Terms
| Term | Definition |
|------|-----------|
| DDoS | Distributed denial-of-service: many sources flood a target to exhaust capacity |
| Cloud Armor | Google's WAF + DDoS protection attached to load balancers |
| Network access control | Policies deciding which sources may reach which resources, on which ports |
| Firewall policy | Group of firewall rules applied at org/folder (hierarchical) or network/region level |
| Cloud IDS | Managed intrusion detection based on mirrored traffic |
| Security monitoring | Collecting and analyzing logs/telemetry to detect and respond to threats |

---

## Real-World Connections
- **Network+ Domain 4:** DDoS, reflection and amplification ([[L19 - Network Attacks]]); ACLs, zones, NAC, hardening ([[L20 - Network Defense Techniques]]); CIA triad and IAM ([[L18 - Basic Network Security]]). This course is the cloud implementation of those objectives.
- **Your cert path:** maps directly onto **AZ-500** (Azure DDoS Protection, NSGs/Azure Firewall, Defender for Cloud) and **KCSA/CKS** (NetworkPolicies, runtime detection with Falco).
- **Wireshark course:** Cloud IDS and Packet Mirroring feed the same traffic you'd analyze in Wireshark ([[WS03 - Where to Capture on the Network]]).
- **k3s:** Ingress + rate limiting ≈ Cloud Armor; NetworkPolicies ≈ firewall policies; Kubeshark/Falco ≈ monitoring and detection.

---

## Troubleshooting Scenario
**Scenario (course preview):** A public e-commerce API on a global load balancer slows to a crawl during a sale. Traffic spikes from thousands of IPs hitting `/login`. The backends are healthy but saturated.
**Direction:** an **L7 DDoS / credential-stuffing** pattern. Module 1's answer is **Cloud Armor**: rate-based rules on `/login`, Adaptive Protection to suggest signatures, and preconfigured WAF rules. Module 3 adds **logging and analysis** to confirm and tune.

---

## Hands-On Ideas
1. List current firewall posture in a test project: `gcloud compute firewall-rules list --format="table(name,network,direction,sourceRanges.list(),allowed[].map().firewall_rule().list(),targetServiceAccounts.list())"`. Flag any `0.0.0.0/0` ingress rules.
2. Enable **Firewall Rules Logging** on one rule and **VPC Flow Logs** on one subnet, so you have data for Module 3.
3. Map each Network+ L19 attack (SYN flood, amplification, HTTP flood) to the Google Cloud control you expect in Module 1.

---

## Quiz
1. What are the three modules of Course 4?
   - A) Routing, DNS, NAT  B) DDoS protection; Controlling access to VPC networks; Advanced security monitoring and analysis  C) Load balancing, CDN, Interconnect  D) IAM, billing, quotas
2. Which CIA pillar does DDoS protection primarily preserve?
   - A) Confidentiality  B) Integrity  C) Availability  D) Non-repudiation
3. "Define who can access your resources and how" describes which module?
   - A) DDoS protection  B) Controlling access to VPC networks  C) Monitoring and analysis  D) Hybrid connectivity

**Answers:** 1-B, 2-C, 3-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP22 - Best Practices for Connectivity and Topologies]] — End of Course 3
- [[L18 - Basic Network Security]] · [[L19 - Network Attacks]] · [[L20 - Network Defense Techniques]]
- [[certs/gcp-networking]]
