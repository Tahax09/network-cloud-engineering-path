---
id: GCP10
aliases: [GCP10]
title: Private Services Access
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Private Connection Options
date: 2026-10-09
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, private-services-access, psa, vpc-peering, service-networking-api, allocated-range, cloud-sql, apigee, cloud-tpu, custom-route-export, servicenetworking]
---

# GCP10 — Private Services Access (PSA)

## Summary
**Private Services Access** connects your VPC to a **service producer's VPC** (Google-managed, created for you) using **VPC Network Peering**, over an **IPv4 range you allocate** in your own VPC. It's how **Cloud SQL private IP**, Memorystore, Filestore, Apigee, Cloud TPU, and Vertex AI-type services get internal addresses. Key facts: **one allocated range per producer network**, **one private connection per producer** (even for multiple services), and **all VPC peering limits apply**, including **non-transitivity**. So on-prem access needs **custom route export** plus advertising the range to on-prem.

---

## Core Concepts

### Scenario (Cymbal ML API)
A proprietary ML API in a private subnet must use **Cloud SQL** and Google's AI platform privately, without any public-internet exposure. PSA gives those managed services **internal IPs inside a range you control**, reachable over peering.

### How it works
```
Your VPC (consumer)                              Google-created producer project/VPC
  allocated range 10.240.0.0/16  ──VPC Network Peering──▶  subnets carved from 10.240.0.0/16 per service/region
  VM ──▶ 10.240.0.2 (Cloud SQL) ─────────────────────────▶  Cloud SQL instance 10.240.0.2
```
1. **Allocate** an internal IPv4 range in your VPC (`--purpose=VPC_PEERING`).
2. Create the **private connection** (`gcloud services vpc-peerings connect`). Google creates a **producer project** for you.
3. Each Google service creates a **subnet from your allocated block** in the region where you provision resources.
4. Traffic to e.g. `10.240.0.2` routes over the peering. **Subnet routes are exchanged automatically**, with no manual import/export for subnet routes (except privately used public IP ranges).
5. **Cross-region:** if the service supports it, VMs in any region can reach resources in other regions.

### Requirements and setup rules
| Rule | Detail |
|------|--------|
| **Supported services only** | Apigee, Cloud SQL, Cloud TPU, Memorystore, Filestore, AlloyDB… (check the docs list) |
| **Service Networking API** | Must be enabled in **both** consumer and producer projects |
| **Producer onboarding** | One-time process for producers that offer PSA (contact Google). Relevant to third-party producers; Google services are pre-onboarded |
| **Producer range** | Producer allocates a range in its VPC used for each consumer connection |
| **Consumer ranges** | **One allocated range per producer VPC network** (3 producers = 3 ranges) |
| **One connection per producer** | Using Cloud SQL **and** Cloud TPU (same producer, `servicenetworking`) = **one** private connection |
| **Disable ≠ delete** | Disabling the PSA connection **doesn't delete the peering** and **doesn't release the allocated range**. Both must be removed explicitly |

### Caveats
- **On-prem access needs custom route export.** Peering is non-transitive, so the producer VPC doesn't learn your on-prem routes by default. Enable **export custom routes** on the `servicenetworking-googleapis-com` peering, and have **Cloud Router advertise the allocated range** to on-prem.
- **Not all services are supported.**
- **VPC Peering quotas/limits apply** (peering connections per network, routes, etc.), plus every peering restriction: non-transitive, **no overlapping ranges**.

> **Transcript correction:** The lecture says "with Private Service Connect, you had to import and export routes between the consumer and producer VPC networks." That's backwards. **PSC exchanges no routes at all** (endpoint + service attachment). Route import/export is a **VPC Network Peering** concept, and with PSA, **subnet routes** are exchanged automatically. **Custom** routes (on-prem, static) need explicit export.
>
> "VPC network period connection" in the debrief = **VPC Network Peering** connection.

### Module comparison (from the lecture's closing table)
| Feature | Purpose |
|---------|---------|
| **Private Google Access** | VMs without public IPs reach **Google APIs and services** |
| **Private Service Connect** | Expose your own, Google, or third-party services to consumers, **even outside Google Cloud** (endpoint-based, no peering) |
| **Private Services Access** | Private access to **Google/third-party managed services placed in a peered producer VPC** |

---

## Key Terms

| Term | Definition |
|------|-----------|
| Private Services Access (PSA) | Peering-based private connection to a service producer VPC |
| Allocated range | Internal IPv4 block you reserve in your VPC for producer resources |
| Private connection | The PSA connection (implemented as VPC peering) to a producer |
| Service Networking API | `servicenetworking.googleapis.com`; required on both sides |
| Producer onboarding | One-time setup for a producer to offer PSA |
| `servicenetworking-googleapis-com` | Name of the peering Google creates for PSA |
| Custom route export | Peering setting to share static/dynamic (e.g., on-prem) routes with the peer |
| Non-transitive peering | A peer's peers/on-prem aren't reachable without explicit route export |

---

## Real-World Connections

**AWS comparison:** AWS has no "peer into the provider's VPC" model for managed DBs. RDS/ElastiCache ENIs live **directly in your subnets** (DB subnet groups). The PSA allocated range ≈ choosing which subnets RDS uses. The PSA peering non-transitivity is like **AWS VPC peering** (also non-transitive, no overlapping CIDRs).

**Network+ links:** Allocating a non-overlapping range is **IPAM/subnetting** discipline ([[L07 - IP Addressing]]). Exporting custom routes so on-prem can reach the producer is **route advertisement** ([[L09 - Routing Technologies]]). "Disable doesn't release the range" is **change management/documentation** ([[L13 - Organizational Processes and Procedures]]): track what's actually deleted.

**k3s / Kubernetes:** GKE **VPC-native** clusters plus a Cloud SQL private IP via PSA is the standard pattern for apps like your Next.js + PostgreSQL stack: the app pod reaches `10.240.0.2:5432` over peering. **Plan your allocated range not to overlap** your k3s pod/service CIDRs (10.42/16, 10.43/16) if you ever bridge the lab to GCP over VPN.

---

## Troubleshooting Scenario

**Scenario:** On-prem servers (connected via HA VPN + Cloud Router) must query a Cloud SQL private IP `10.240.0.2`. GCE VMs in the VPC connect fine; on-prem times out.

**Analysis:**
1. **On-prem → producer:** does on-prem have a route to 10.240.0.0/16? Cloud Router by default advertises only **subnet routes** of the VPC. The PSA allocated range isn't a local subnet, so it's **not advertised**. → Add a **custom advertisement** for 10.240.0.0/16.
2. **Producer → on-prem (return path):** the producer VPC only knows *your subnet routes* via peering. On-prem routes are **dynamic/custom**, so the producer can't route replies back. → **Export custom routes** on the PSA peering.

**Fix:**
```
gcloud compute routers update onprem-router --region=REGION --advertisement-mode=CUSTOM \
  --set-advertisement-groups=ALL_SUBNETS --set-advertisement-ranges=10.240.0.0/16
gcloud compute networks peerings update servicenetworking-googleapis-com \
  --network=VPC --export-custom-routes
```
**Verify:** `gcloud compute networks peerings list-routes servicenetworking-googleapis-com --network=VPC --region=REGION --direction=OUTGOING` shows on-prem prefixes. `psql -h 10.240.0.2` works from on-prem.

---

## Hands-On Ideas
1. Configure PSA end to end:
   ```bash
   gcloud services enable servicenetworking.googleapis.com
   gcloud compute addresses create google-managed-services --global \
     --purpose=VPC_PEERING --prefix-length=16 --network=VPC
   gcloud services vpc-peerings connect --service=servicenetworking.googleapis.com \
     --ranges=google-managed-services --network=VPC
   ```
   Then create a Cloud SQL Postgres instance with **private IP only** and connect from an internal-only VM.
2. Run `gcloud compute networks peerings list` and find `servicenetworking-googleapis-com`.
3. Disable the connection, then check that the peering and allocated range still exist, which confirms the "disable ≠ delete" rule.

---

## Quiz

1. How does Private Services Access implement the connection between the consumer and producer?
   - A) PSC endpoint  B) VPC Network Peering  C) Cloud VPN  D) Cloud NAT

2. A consumer uses services from three different producer VPC networks via PSA. How many allocated ranges are needed?
   - A) One  B) Two  C) Three  D) None

3. A consumer uses Cloud SQL and Cloud TPU from the same producer. How many private connections are created?
   - A) One  B) Two  C) One per region  D) One per instance

4. Which API must be enabled in both consumer and producer projects?
   - A) Compute Engine API only  B) Service Networking API  C) Cloud DNS API  D) Network Connectivity API

5. After disabling a PSA connection, what remains?
   - A) Nothing; everything is deleted  B) The peering and the allocated IP range remain until explicitly removed  C) Only the range is released  D) Only the peering is deleted

6. On-prem hosts can't reach a Cloud SQL private IP over HA VPN. VMs in the VPC can. What's required? (Pick the best combination.)
   - A) Enable PGA  B) Advertise the allocated range to on-prem and export custom routes to the producer peering  C) Add Cloud NAT  D) Create a PSC endpoint for Cloud SQL Admin API

7. Which limitation does PSA inherit from VPC peering?
   - A) Overlapping IP ranges are allowed  B) Non-transitivity and peering quotas  C) Only IPv6 is supported  D) Producers can't scale

8. Your VPC and a SaaS producer's VPC overlap in IP space. Should you use PSA?
   - A) Yes, PSA handles overlap  B) No; use Private Service Connect  C) Yes, with Cloud NAT  D) Yes, with BYOIP

**Answers:** 1-B, 2-C, 3-A, 4-B, 5-B, 6-B, 7-B, 8-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP07 - Private Connection Options - Overview]] — Overview · [[GCP08 - Private Google Access]] — PGA · [[GCP09 - Private Service Connect]] — PSC · [[GCP11 - Cloud NAT]] — Cloud NAT
- [[GCP01 - Routes and Route Preferences]] — Routes, custom/dynamic routes, Cloud Router
- [[concepts/gcp-private-access]]
- [[certs/gcp-networking]]
