---
id: GCP48
aliases: [GCP48]
title: Load Balancer Optimization Strategies
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: "Course 5: Load Balancing — Module 2: Caching and Optimizing Load Balancing"
date: 2026-10-22
status: Complete
quiz_attempted: Yes
weak_areas: []
tags: [gcp, cost-optimization, autoscaling, right-sizing, cloud-monitoring, cloud-billing, labels, committed-use-discounts, spot-vms, cloud-cdn]
---

# GCP48 — Load Balancer Optimization Strategies

> 🎛️ **Interactive visualization:** [Edge Cache and Armor](../../visualizations/gcp/GCP45-48%20-%20Edge%20Cache%20and%20Armor.html), "Cost levers" tab: see how hit ratio and autoscaling change origin load. Opens in your browser.

## Summary
Cost optimization without hurting performance or availability. The lecture groups it into four areas:

| Area | Strategy | Google Cloud mechanism |
|------|----------|----------------------|
| **Autoscaling** | Scale backends with real demand; clear up/down thresholds; scale on custom metrics (CPU, response time) | MIG autoscaler (CPU, **LB serving capacity**, Cloud Monitoring metrics, schedules); GKE HPA/cluster autoscaler |
| **Right-sizing** | Right **LB type** (don't pay for L7 if passthrough L4 suffices); right machine type; review utilization | LB families ([[GCP40 - Module Introduction - Hybrid Load Balancing and Traffic Management]]); **Recommender** machine-type and idle-VM recommendations |
| **Monitoring and cost tools** | Cloud Monitoring metrics; Cloud Billing reports and cost management; **cost allocation** by team/project | Monitoring dashboards; Billing export to BigQuery; **labels** for allocation |
| **Additional** | Scheduled downtime for non-critical workloads; discounts for predictable load; cheap capacity for interruptible work; **Cloud CDN** to offload origins | Instance schedules; **committed use discounts**; **Spot VMs**; Cloud CDN ([[GCP45 - Cloud CDN]]) |

GQ08 Q2's answer: **a CDN caching layer** reduces origin and LB load. The wrong options (most expensive LB, over-provisioning, ignoring health checks) are anti-patterns.

> **Terminology fix:** the lecture uses AWS terms. In Google Cloud:
> - "Reserved instances" → **committed use discounts (CUDs)**, which apply to compute (vCPU/memory), not to load balancers
> - "Spot instances" → **Spot VMs**
> - "Cost allocation tags" → **labels** (resource tags in GCP are for IAM and firewall policy, not billing breakdowns)

---

## Core Concepts

### Autoscale on the load balancer's own signal
```bash
gcloud compute instance-groups managed set-autoscaling web-mig --region=us-central1 \
    --min-num-replicas=2 --max-num-replicas=20 \
    --target-load-balancing-utilization=0.8 --cool-down-period=90
gcloud compute backend-services update web-bes --global --balancing-mode=RATE --max-rate-per-instance=100 \
    --capacity-scaler=1.0
```
- **LB utilization** targets a fraction of the backend's configured serving capacity (`--max-rate-per-instance`).
- **Thresholds and cool-down** prevent flapping (scale events that undo each other).
- **Custom metrics:** latency, queue depth via Cloud Monitoring (`--custom-metric-utilization`).

### Cost levers ranked by typical impact
| Lever | Saves | Risk if overdone |
|-------|-------|-----------------|
| Cloud CDN hit ratio ↑ | Origin compute + **egress** | Stale content: use TTLs and invalidation |
| Autoscaling min replicas ↓ | Idle compute | Cold starts at a spike: keep min ≥ 2 across zones |
| Right LB type | LB rule and processing charges | Losing features (WAF, URL maps) you actually need |
| CUDs | Up to ~55–70% on steady compute | Paying for unused commitment |
| Spot VMs | Up to ~60–91% | Preemption: non-critical, stateless only |
| Labels + Billing export | Visibility (enables all the above) | None |

---

## Key Terms
| Term | Definition |
|------|-----------|
| Autoscaling policy | Rules (target metric, min/max, cool-down) for MIG size |
| Capacity scaler | Backend-service knob (0–1) scaling a backend's usable capacity |
| Committed use discount | 1- or 3-year commitment for discounted compute |
| Spot VM | Preemptible, deeply discounted VM |
| Label | Key-value on resources, exported with billing data |

## Real-World Connections
- **k3s / your stack:** HPA on request rate ≈ MIG autoscaling on LB utilization; Kibana dashboards ≈ Cloud Monitoring; namespace labels for chargeback ≈ GCP labels.
- **AWS:** ASG target tracking on `ALBRequestCountPerTarget`, Savings Plans/RIs, Spot, cost allocation tags.

## Troubleshooting Scenario
**Scenario:** The bill doubled after a launch; the LB shows a steady 30% utilization, but the MIG sits at max replicas.
**Cause:** the autoscaler targets **CPU**, and a memory leak or background job pins CPU. The LB metric shows there's spare serving capacity.
**Fix:** autoscale on **LB utilization** or request latency, fix the job, and add the label `team=checkout` plus a billing budget alert.

## Hands-On Ideas
1. Export billing to BigQuery and query cost by the label `env`.
2. Put a MIG on `--target-load-balancing-utilization=0.6`, run a load test (hey/wrk), and watch the scale-out/scale-in with cool-down.

## Quiz
1. Which practice reduces load balancing cost?
   - A) Overprovision for peak  B) Pick the most expensive LB type  C) Add a CDN caching layer  D) Disable health checks
2. GCP's term for "reserved instances" is:
   - A) Sole-tenant nodes  B) Spot VMs  C) Preemptible LBs  D) Committed use discounts
3. Billing breakdown by team in GCP uses:
   - A) Labels  B) Secure tags  C) Network tags  D) Service accounts
4. Which autoscaling signal comes directly from the load balancer?
   - A) Load balancing serving utilization  B) Disk usage  C) Memory  D) Uptime checks

**Answers:** 1-C, 2-D, 3-A, 4-A

## Weak Areas to Revisit
*(none flagged yet)*

## Linked Pages
- [[GCP45 - Cloud CDN]] · [[GCP44 - Internal Network Load Balancers as Next Hops]] · [[GQ08_Caching_and_Optimizing_Load_Balancing]] · [[certs/gcp-networking]]
