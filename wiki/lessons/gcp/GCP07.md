---
id: GCP07
title: Private Connection Options — Overview
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Private Connection Options
date: 2026-10-09
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, private-access, private-google-access, private-service-connect, private-services-access, serverless-vpc-access, cloud-nat, internal-ip]
---

# GCP07 — Private Connection Options: Overview

## Summary
This lesson opens a new module. Its question: how do VMs and on-prem hosts with **internal IPs only** reach Google APIs, managed services, and third-party services without going over the public internet? Google offers several **independent** options, and you can enable any or all of them. This lesson is the map; the following lessons cover each one ([[GCP08]] Private Google Access, then Private Service Connect, Private Services Access, Cloud NAT + lab). Key exam skill: **pick the right option for a scenario.**

---

## Core Concepts

### Why private access
- Uses **internal IP addresses** end to end, so consumers reach APIs and services over an internal connection.
- Traffic **doesn't traverse the public internet** (unless the consumer itself connects to Google Cloud over an external connection).
- **Faster and more secure**: no external IPs on VMs, so the attack surface is smaller.
- **All Google Cloud APIs and services support private access.** You can also publish *your own* services privately.
- Consumers can be in **Google Cloud, other public clouds, or on-premises**.

Google APIs have public URLs (`*.googleapis.com`) that are reachable on the internet by default. Private access options let internal-only VMs reach them anyway.

### The Options at a Glance

| Option | What it connects | Mechanism | Who it's for |
|--------|-----------------|-----------|--------------|
| **Private Google Access (PGA)** | Internal-only VMs → **Google APIs** (Cloud Storage, BigQuery…) | **Per-subnet toggle**. Traffic to Google API IPs leaves via the `default-internet-gateway` route but **stays on Google's network** | VMs with no external IP ([[GCP08]]) |
| **PGA for on-premises hosts** | On-prem hosts → Google APIs | **Cloud VPN / Interconnect** into the VPC + DNS to `private`/`restricted.googleapis.com` + Cloud Router custom route advertisement | Hybrid networks ([[GCP08]]) |
| **Private Service Connect (PSC)** | Consumer VPC → **Google APIs** or a **Google/third-party/your own producer service** via a **service attachment** | An **endpoint** (an internal IP in *your* subnet, a forwarding rule) that maps to the service. **No VPC peering** | SaaS/partner services, multi-tenant producers, overlapping IP ranges |
| **Private Services Access (PSA)** | Your VPC → a **service producer VPC created exclusively for you** (Cloud SQL, Memorystore, Filestore…) | **VPC Network Peering** with an **allocated IP range** you reserve | Google-managed services that run inside a Google-owned VPC |
| **Serverless VPC Access** | **Cloud Run, App Engine standard, Cloud Functions** → your VPC resources by internal IP | Connector (managed VM group) in a /28; Cloud Run now also offers **Direct VPC egress** | Serverless workloads reaching VPC resources (not covered in this module) |
| **Cloud NAT** | Internal-only VMs → **the internet** (outbound only) | Managed, software-defined NAT on Cloud Router | OS updates, external APIs, anything that isn't Google API traffic |

### How to choose
```
Destination = Google APIs (storage.googleapis.com, BigQuery)?
   ├─ from a VM with no external IP ..................... Private Google Access
   ├─ from on-premises over VPN/Interconnect ............ PGA for on-prem hosts (or a PSC endpoint)
   └─ need a custom internal IP / per-VPC endpoint ...... PSC endpoint for Google APIs
Destination = a managed service in a Google producer VPC (Cloud SQL private IP)?
   └─ ....................................................... Private Services Access (peering)
Destination = a partner/SaaS/another team's service, IP ranges may overlap?
   └─ ....................................................... Private Service Connect (service attachment)
Source = Cloud Run / Functions / App Engine → VPC?
   └─ ....................................................... Serverless VPC Access / Direct VPC egress
Destination = public internet (apt repos, GitHub, 3rd-party SaaS APIs)?
   └─ ....................................................... Cloud NAT
```

> **Transcript clarification:** The lecture says PGA for on-premises hosts connects "through the default Internet gateway of the VPC network." That means the VPC's **route** to the Google API range has the next hop `default-internet-gateway`. The packets **don't cross the public internet**: they enter Google over **Cloud VPN or Interconnect** and are delivered over Google's network. On-prem hosts need a VPN/Interconnect connection, not an internet path. Details in [[GCP08]].

### PSC vs PSA (most-confused pair)
| | Private Service Connect | Private Services Access |
|-|------------------------|------------------------|
| Mechanism | **Endpoint** (forwarding rule with an internal IP in your subnet) → service attachment | **VPC Network Peering** to a producer VPC |
| IP planning | Only one IP per endpoint in *your* range; **overlapping ranges OK** | Must **allocate a dedicated range** that can't overlap |
| Transitivity | Endpoint reachable from on-prem/peered networks via your routing | **Peering is non-transitive**: on-prem/peered VPCs can't reach the producer without extra config (custom route export) |
| Direction | Consumer-initiated, producer can't reach into your VPC | Peering = full network-level reachability between the two VPCs (governed by firewall rules) |
| Typical use | Third-party SaaS, publishing your own service, Google APIs via custom IP | Cloud SQL private IP, Memorystore, Filestore, Vertex AI |

---

## Key Terms

| Term | Definition |
|------|-----------|
| Private access | Reaching APIs/services using internal IPs, without the public internet |
| Private Google Access | Per-subnet setting letting internal-only VMs reach Google APIs |
| Private Service Connect (PSC) | Consumer endpoint (internal IP) mapped to a Google API bundle or a producer's service attachment |
| Service attachment | Producer-side PSC resource that publishes a service |
| Private Services Access (PSA) | VPC peering to a Google-managed producer VPC dedicated to you |
| Allocated IP range | Range you reserve for PSA producer resources |
| Serverless VPC Access | Connector letting serverless products send to VPC internal IPs |
| Cloud NAT | Managed outbound NAT for internal-only VMs to reach the internet |
| Service producer / consumer | Side publishing a service / side using it |

---

## Real-World Connections

**AWS equivalents** (see [[concepts/vpc-and-subnets]]):
| GCP | AWS |
|-----|-----|
| Private Google Access | **Gateway VPC endpoint** (S3/DynamoDB) + interface endpoints for other AWS APIs |
| Private Service Connect | **AWS PrivateLink** (interface endpoints ↔ endpoint services) |
| Private Services Access | Closest: RDS/ElastiCache placed in your subnets (AWS has no peering-to-producer model) |
| Serverless VPC Access | Lambda VPC configuration (ENIs in your subnets) |
| Cloud NAT | **NAT Gateway** |

**Network+ link:** This is the cloud version of **screened subnet / zero trust** thinking from [[L18]]–[[L20]]: no public IPs on workloads, and only controlled, specific paths out. Cloud NAT is **PAT** ([[L09]]).

**k3s connection:** If you ran k3s nodes on GCE with **no external IPs** (good hardening), you'd need **PGA** to pull images from Artifact Registry (`*-docker.pkg.dev`) and to write Elasticsearch snapshots to Cloud Storage, plus **Cloud NAT** for `get.k3s.io`, GitHub releases, and Docker Hub. That's exactly how GKE private clusters work.

---

## Troubleshooting Scenario

**Scenario:** A VM with only an internal IP can `curl https://storage.googleapis.com` successfully but `apt update` times out.

**Analysis:** PGA is enabled on the subnet, so Google API traffic works. `apt` mirrors (archive.ubuntu.com) are **not Google APIs**, and PGA only covers Google APIs and services.
**Fix:** Configure **Cloud NAT** for the subnet's region (or use a Google-hosted mirror / Artifact Registry remote repository for apt packages).

---

## Hands-On Ideas
1. In a test project, create a custom-mode VPC with two subnets. Enable PGA on one only (`gcloud compute networks subnets update SUBNET --region=R --enable-private-ip-google-access`) and compare behavior from internal-only VMs in each.
2. Build the comparison table above from memory: for each option, write mechanism + one use case.
3. Map your k3s lab dependencies into two lists: "Google APIs" (PGA) vs "internet" (Cloud NAT).

---

## Quiz

1. A VM with no external IP must read objects from Cloud Storage. What's the simplest option?
   - A) Cloud NAT  B) Private Google Access on the VM's subnet  C) Private Services Access  D) VPC peering

2. Your application must connect privately to a third-party SaaS provider's service published on Google Cloud. Your VPC's IP ranges overlap with the provider's. Which option fits?
   - A) Private Services Access  B) VPC Network Peering  C) Private Service Connect  D) Private Google Access

3. Which private access option is implemented as a VPC Network Peering connection to a producer network created exclusively for you?
   - A) Private Service Connect  B) Private Services Access  C) Cloud NAT  D) Serverless VPC Access

4. Internal-only VMs need to download packages from a public GitHub repository. Which option?
   - A) Private Google Access  B) Cloud NAT  C) Private Service Connect  D) Private Services Access

5. A Cloud Run service must connect to a Redis instance's internal IP in your VPC. Which option?
   - A) Serverless VPC Access (or Direct VPC egress)  B) Private Google Access  C) Cloud NAT  D) BYOIP

6. Can you use Private Google Access, Private Service Connect, and Cloud NAT in the same VPC?
   - A) No, they are mutually exclusive  B) Yes, they operate independently  C) Only PGA and NAT together  D) Only in auto-mode VPCs

7. What is the AWS equivalent of Private Service Connect?
   - A) NAT Gateway  B) AWS PrivateLink  C) Internet Gateway  D) Transit Gateway

**Answers:** 1-B, 2-C, 3-B, 4-B, 5-A, 6-B, 7-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP08]] — Private Google Access (detailed)
- [[GCP01]] — Routes, default internet gateway next hop
- [[GCP04]] — Cloud DNS private zones (used for googleapis.com overrides)
- [[concepts/gcp-private-access]] — Comparison reference for all options
- [[concepts/vpc-and-subnets]] — AWS VPC endpoints, NAT gateway
- [[certs/gcp-networking]]
