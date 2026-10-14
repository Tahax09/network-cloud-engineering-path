---
id: GCP08
aliases: [GCP08]
title: Private Google Access
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Private Connection Options
date: 2026-10-09
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, private-google-access, pga, googleapis, private-googleapis, restricted-googleapis, vpc-service-controls, cloud-router, custom-route-advertisement, cloud-dns, private-zone, cloud-vpn, default-internet-gateway, ipv6]
---

# GCP08 — Private Google Access

## Summary
**Private Google Access (PGA)** lets VMs with **only internal IPs** reach **Google APIs and services** (Cloud Storage, BigQuery…) without external IPs or NAT. It is enabled **per subnet** and has **no effect on VMs that have external IPs**. The extended form, **PGA for on-premises hosts**, sends on-prem traffic over **Cloud VPN/Interconnect** to the special VIP ranges `private.googleapis.com` (199.36.153.8/30) or `restricted.googleapis.com` (199.36.153.4/30). That needs three things to line up: **DNS**, **routes** (VPC + Cloud Router advertisement), and **firewall egress**.

---

## Core Concepts

### The problem (Cymbal Corp scenario)
A recommendation engine on GCE VMs processes sensitive customer data and needs **BigQuery** and **Cloud Storage**. Internal fleets (inventory, POS) need updates but not general internet access.
| Traditional option | Downside |
|-------------------|----------|
| External IPs on VMs | Exposes VMs to the internet, larger attack surface |
| NAT setup | Management overhead, a potential bottleneck |
| **Private Google Access** | Internal-only VMs reach Google APIs; no exposure, no NAT |

### How PGA behaves (subnet toggle)
```
gcloud compute networks subnets update subnet-a \
    --region=us-central1 --enable-private-ip-google-access
```

**Sample topology:** PGA on subnet A, off on subnet B.
| VM | Subnet | External IP? | Reach Google APIs? | Why |
|----|--------|-------------|-------------------|-----|
| A1 | A (PGA **on**) | No | ✅ | PGA applies to internal-only VMs |
| B1 | B (PGA **off**) | No | ❌ | Internal-only + no PGA = **traffic stays inside the VPC** |
| A2 | A | **Yes** | ✅ | Uses its external IP; PGA irrelevant |
| B2 | B | **Yes** | ✅ | Uses its external IP; PGA irrelevant |

**Rules to memorize:**
- PGA is **per subnet**, not per VPC or per VM.
- **PGA has no effect on VMs with external IPs.**
- With PGA off, internal-only VMs can only send traffic **within the VPC** (unless Cloud NAT is configured, covered later in the module).
- PGA covers **Google APIs and services only**, not the general internet (apt mirrors, GitHub) → that's **Cloud NAT**.

### PGA for On-Premises Hosts (Cymbal Bank, Tokyo)
On-prem hosts reach Google APIs using internal routing over **Cloud VPN** (or Interconnect).

```
On-prem host
  │ DNS: *.googleapis.com → CNAME restricted.googleapis.com → 199.36.153.4/30
  │ Route: 199.36.153.4/30 learned from Cloud Router (custom route advertisement) → via VPN
  ▼
Cloud VPN tunnel ──▶ VPC network
                       │ custom static route: 199.36.153.4/30 → next hop default-internet-gateway
                       ▼
               Google's network ──▶ API / service (stays on Google backbone, not the public internet)
```

**The three configuration pieces:**
| Piece | Where | What |
|-------|-------|------|
| **DNS** | On-prem DNS (and/or a Cloud DNS **private zone** for `googleapis.com` authorized for the VPC) | Map `*.googleapis.com` → `restricted.googleapis.com` (or `private.googleapis.com`) A records 199.36.153.4–.7 (or .8–.11) |
| **Advertise the range to on-prem** | **Cloud Router** | **Custom route advertisement** of 199.36.153.4/30 so on-prem sends that traffic into the tunnel |
| **VPC route** | VPC network | **Custom static route** 199.36.153.4/30 → next hop **`default-internet-gateway`** (the special next hop that delivers to Google APIs; if you deleted the default route, you must add this) |
| (+ firewall) | VPC | **Egress** allowed to the range (the implied allow-egress rule covers this unless you've added deny rules) |

### `private.googleapis.com` vs `restricted.googleapis.com`
| | private.googleapis.com | restricted.googleapis.com |
|-|----------------------|--------------------------|
| IPv4 range | **199.36.153.8/30** | **199.36.153.4/30** |
| IPv6 range | 2600:2d00:0002:2000::/64 | 2600:2d00:0002:1000::/64 |
| APIs reachable | **Most** Google APIs | **Only APIs supported by VPC Service Controls** |
| Use when | General private access | You need **VPC-SC** data exfiltration protection |

> **Transcript note:** The lecture only shows `restricted.googleapis.com`. It warns that "only the supported APIs are accessible to this configuration, which might cause other services to be unreachable." That's the trade-off: restricted = VPC-SC-supported APIs only. If an app needs an API outside that set, use `private.googleapis.com`.

**Cloud DNS limitation:** A Cloud DNS private zone for `googleapis.com` **overrides the whole domain**. Cloud DNS doesn't support **partial overrides** (e.g., only `storage.googleapis.com`), so every googleapis.com name must be covered by your records (typically a wildcard CNAME `*.googleapis.com → restricted.googleapis.com`). If you need partial overrides, run **BIND** (or another DNS server that supports them).

### Caveats / requirements
1. **VPC network required.** Legacy networks have no subnets, so there's nowhere to toggle PGA.
2. **Enable the APIs** you want (APIs & Services page).
3. **Routes and egress firewall** must allow the destination ranges used by Google APIs. Keep the default route, or add specific routes to `default-internet-gateway`.
4. Using `private.` or `restricted.googleapis.com` → **you must create DNS records** for them.
5. Those domains offer **IPv4 only**. **IPv6 PGA:** the VM must have a **/96 IPv6 range** (dual-stack subnet, [[GCP02 - IPv6 in Google Cloud]]), its software must send from an address in that range, and it sends to the IPv6 addresses of the **default domains** (or the private/restricted IPv6 ranges).

> **Transcript cleanup:** "BMs"/"BPC" = VMs/VPC; "next hub" = **next hop**; "Cymbol" = Cymbal (Google's fictional company). The on-prem example mentions Cloud SQL: PGA reaches the **Cloud SQL Admin API**. Connecting to a Cloud SQL instance's **private IP** uses **Private Services Access** ([[GCP07 - Private Connection Options - Overview]]).

---

## Key Terms

| Term | Definition |
|------|-----------|
| Private Google Access (PGA) | Per-subnet setting letting internal-only VMs reach Google APIs |
| PGA for on-premises hosts | On-prem access to Google APIs over VPN/Interconnect via special VIPs |
| default-internet-gateway | Special route next hop; for Google API ranges, traffic stays on Google's network |
| private.googleapis.com | 199.36.153.8/30, most Google APIs |
| restricted.googleapis.com | 199.36.153.4/30, VPC Service Controls-supported APIs only |
| VPC Service Controls | Service perimeter preventing data exfiltration from Google APIs |
| Custom route advertisement | Cloud Router advertising a range (e.g., 199.36.153.4/30) to on-prem over BGP |
| Cloud DNS private zone | Zone visible only to authorized VPCs; used to override googleapis.com |
| Partial override | Overriding only some names in a domain. Unsupported in Cloud DNS, so use BIND |
| Legacy network | Pre-VPC network without subnets; PGA unsupported |

---

## Real-World Connections

**AWS comparison:**
| GCP PGA | AWS |
|---------|-----|
| Subnet toggle, Google APIs via default-internet-gateway on Google's network | **Gateway VPC endpoint** for S3/DynamoDB (route table entry, free); **interface endpoints** (PrivateLink) for other AWS APIs |
| restricted.googleapis.com + VPC-SC | **VPC endpoint policies** + S3 bucket policies with `aws:SourceVpce` |
| On-prem via VPN + Cloud Router advertisement | On-prem → Direct Connect/VPN → **interface endpoint** (gateway endpoints aren't reachable from on-prem) |

**Network+ links:** Custom route advertisement is **BGP** ([[L09 - Routing Technologies]]). The DNS override is a split-horizon / private zone pattern ([[L16 - IP Services]]). Removing external IPs is **attack surface reduction** / hardening ([[L20 - Network Defense Techniques]]).

**k3s / GKE:** GKE **private clusters** require PGA on the node subnet so nodes can pull from Artifact Registry and send logs and metrics to Cloud Logging/Monitoring. A self-managed k3s on internal-only GCE VMs needs the same, plus Cloud NAT for non-Google registries (Docker Hub, `ghcr.io`).

---

## Troubleshooting Scenario

**Scenario:** On-prem servers in Tokyo should reach Cloud Storage privately over Cloud VPN. `nslookup storage.googleapis.com` on-prem returns `199.36.153.5`, but `curl https://storage.googleapis.com` times out. The VPN tunnel is up.

**Analysis ([[L21 - Network Troubleshooting Methodology]] methodology):**
1. **DNS ✔** resolves to the restricted VIP.
2. **On-prem routing?** `show ip route 199.36.153.4` on the on-prem router: **no route**. It's following the default route to the internet, where 199.36.153.4/30 isn't reachable.
3. **Cause:** Cloud Router is only advertising subnet routes (default advertisement mode); the custom range was never added.

**Fix:**
```
gcloud compute routers update tokyo-router --region=asia-northeast1 \
  --advertisement-mode=CUSTOM --set-advertisement-groups=ALL_SUBNETS \
  --set-advertisement-ranges=199.36.153.4/30
```
**Verify:** the on-prem router learns 199.36.153.4/30 via BGP and `curl` works. Also confirm the VPC has the static route `199.36.153.4/30 → default-internet-gateway` (needed if the 0.0.0.0/0 default route was deleted for hardening).
**Second check:** if a *different* API fails while Storage works, that API may not be VPC-SC-supported, so switch to `private.googleapis.com`.

---

## Hands-On Ideas
1. **Subnet A/B experiment:** two subnets, PGA on A only, internal-only VMs in each. Run `gsutil ls gs://<bucket>` (or `gcloud storage ls`) from both. B fails/hangs.
2. **External IP override:** add an external IP to the VM in subnet B, rerun, and it works. That confirms "PGA has no effect on VMs with external IPs."
3. **DNS override:** create a Cloud DNS private zone `googleapis.com.` with `*.googleapis.com CNAME private.googleapis.com.` and A records 199.36.153.8–.11. Authorize the VPC, then `dig storage.googleapis.com` from a VM.
4. **Route dependency:** delete the default route in a test VPC, confirm PGA breaks, then add `199.36.153.8/30 → default-internet-gateway` and confirm it's restored.

---

## Quiz

1. At what level is Private Google Access enabled?
   - A) VPC network  B) Subnet  C) VM instance  D) Project

2. A VM has an external IP in a subnet where PGA is disabled. Can it reach Cloud Storage?
   - A) No, PGA is required  B) Yes, it uses its external IP; PGA has no effect  C) Only with Cloud NAT  D) Only via PSC

3. A VM with only an internal IP is in a subnet with PGA disabled and no Cloud NAT. Where can it send traffic?
   - A) Anywhere on the internet  B) Only Google APIs  C) Only within the VPC network  D) Nowhere

4. Which IP range does `restricted.googleapis.com` use?
   - A) 199.36.153.8/30  B) 199.36.153.4/30  C) 169.254.169.254/32  D) 35.199.192.0/19

5. What is the key difference between `restricted.googleapis.com` and `private.googleapis.com`?
   - A) Restricted supports IPv6 only  B) Restricted only reaches APIs supported by VPC Service Controls  C) Private requires Interconnect  D) There is no difference

6. On-prem hosts resolve googleapis.com to the restricted VIP, but traffic doesn't enter the VPN tunnel. What is most likely missing?
   - A) A Cloud Router custom route advertisement for 199.36.153.4/30  B) An external IP on the VPN gateway  C) Cloud NAT  D) PGA on a legacy network

7. Inside the VPC, what next hop must the route for 199.36.153.4/30 use?
   - A) The VPN tunnel  B) default-internet-gateway  C) A NAT gateway  D) An internal load balancer

8. You need to override only `storage.googleapis.com` and leave other googleapis.com names untouched. What should you use?
   - A) A Cloud DNS private zone  B) BIND (Cloud DNS doesn't support partial overrides)  C) A response policy on a public zone  D) /etc/hosts on every VM

9. Why can't PGA be used on a legacy network?
   - A) Legacy networks have no subnets  B) Legacy networks lack Cloud Router  C) Legacy networks are IPv6-only  D) PGA requires Interconnect

10. A VM wants to use IPv6 to reach Google APIs. What's required?
    - A) A /96 IPv6 range on the VM's interface, sending from an address in that range  B) restricted.googleapis.com IPv4 only  C) Cloud NAT64  D) An external IPv4 address

**Answers:** 1-B, 2-B, 3-C, 4-B, 5-B, 6-A, 7-B, 8-B, 9-A, 10-A

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP12 - Lab - Implement Private Google Access and Cloud NAT]] — Hands-on lab (PGA + Cloud NAT + IAP)
- [[GCP07 - Private Connection Options - Overview]] — Private connection options overview
- [[GCP01 - Routes and Route Preferences]] — Routes, `default-internet-gateway` next hop, route priority
- [[GCP02 - IPv6 in Google Cloud]] — IPv6 dual-stack subnets (/96 per VM)
- [[GCP04 - Cloud DNS]] — Cloud DNS private zones
- [[L09 - Routing Technologies]] — BGP / route advertisement
- [[L16 - IP Services]] — DNS records, CNAME
- [[concepts/gcp-private-access]] — Option comparison
- [[certs/gcp-networking]]
