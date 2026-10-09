---
id: GCP09
title: Private Service Connect
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Private Connection Options
date: 2026-10-09
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, private-service-connect, psc, psc-endpoint, psc-backend, service-attachment, psc-interface, network-attachment, service-connection-policy, service-class, producer, consumer, multi-tenant, overlapping-ip]
---

# GCP09 — Private Service Connect (PSC)

## Summary
**Private Service Connect** lets consumers reach a service through an **internal IP address in their own VPC**. The service can be Google APIs, a third-party/SaaS service, or another team's service. The consumer creates a **PSC endpoint** (or a **PSC backend** behind a load balancer) that maps to the producer's **service attachment**. There's **no VPC peering** and no route exchange, **consumer and producer IP ranges can overlap**, and connections are **consumer-initiated only**. The reverse direction (producer → consumer) uses a **PSC interface** + **network attachment**. **Service connection policies** automate endpoint creation for managed services.

---

## Core Concepts

### Scenario (Cymbal: financial transaction API)
A bank must expose a sensitive real-time transaction API to partners who may be in other clouds or on-prem. It has to be secure, access-controlled, and scalable.
| Old approach | Problem |
|-------------|---------|
| Public internet exposure | Security risk |
| IP allowlists / VPNs per partner | Cumbersome, doesn't scale |
| **PSC** | Producer **publishes** the API via a service attachment. Each partner creates a **PSC endpoint** in its own VPC. Traffic rides **Google's network**; load balancing happens on the **producer side** |

### Components
```
CONSUMER VPC                                           PRODUCER VPC
VM1 ──▶ PSC endpoint 10.0.20.10 ──(Google backbone)──▶ Service attachment ──▶ forwarding rule
        (forwarding rule, internal IP                       (published service)     (internal LB) ──▶ VM2 / service
         chosen from the consumer's subnet)
```
| Component | Side | Role |
|-----------|------|------|
| **PSC endpoint** | Consumer | Internal IP (forwarding rule) in the consumer VPC that targets a service attachment, or the **Google APIs bundle** (global internal IP) |
| **PSC backend** | Consumer | A PSC **NEG** behind a consumer-side **load balancer**, adding LB features (below) |
| **Service attachment** | Producer | Publishes a service (points at the producer's internal LB forwarding rule); controls which consumers are accepted |
| **PSC NAT subnet** | Producer | Range used to SNAT consumer traffic. **Producer firewall rules only need this range**, not every consumer's subnets |

### Why PSC (vs peering/PSA)
**Consumer benefits:**
- **Chooses its own IP** for each endpoint from its own subnet. No need to reserve ranges for producer backends.
- **Overlapping subnets are fine**, since there's no route exchange.
- **Unidirectional:** only the consumer can initiate. Producers can't reach into the consumer VPC through an endpoint. This simplifies firewalls and blocks rogue producer traffic.

**Producer benefits:**
- **Multi-tenant:** one producer VPC serves many consumer VPCs (thousands), even if consumer ranges overlap.
- Scale backends freely **without asking consumers for IPs**.
- Firewall rules reference only the **PSC NAT range**.

**Performance:** line-rate, enterprise scale. Traffic stays on the Google backbone (except the external ALB case).

> **Transcript inconsistency:** The scenario says partner endpoints get "private IP addresses from a range the financial institution specifies." The lecture later states the correct behavior: **consumers choose the endpoint IP from their own subnet**. The producer controls *who* can connect (accept lists on the service attachment), not consumer IP addressing.

### PSC Backends (via Load Balancers)
Putting a **Google Cloud load balancer** in front (a PSC NEG backend) adds:
- **Custom DNS names** for services and Google APIs (e.g., map an API to `spanner.cymbal.com`). Names and IPs stay internal to the VPC.
- **Traffic control** across endpoints, plus **request logging to Cloud Logging** (proof that traffic stayed in Google Cloud).
- **URL maps** (application LB) to choose which services consumers can reach / route by path.
- **Customer-managed TLS certificates** between client and LB.
- **Data residency in transit:** connect to **regional endpoints** for Google APIs from workloads in the same region.

> **Transcript clarification:** The lecture says regional endpoints let you "be certain that data at rest is stored in the region you configure." Regional endpoints guarantee **in-transit** locality (requests are served in that region). **Data at rest** location is controlled by the resource's own location setting (bucket/dataset region). The lecture also says the ILB and services are "in the us-central1 *zone*". It's the **region**.

**On-prem access:** on-prem reaches endpoints/backends via **Cloud VPN** or **Interconnect VLAN attachments**. In the lecture's example (internal ALB in us-central1), on-prem requests to Cloud Logging, Pub/Sub, and Cloud KMS stay on the Google backbone. Other Google APIs not routed through the LB use their normal (public) path.

**Global external ALB + PSC NEG:** internet consumers hit an **external IP**, and the LB sends traffic through a PSC NEG to the producer's service attachment. This is the **only PSC case where the consumer connects with an external IP.**

### PSC Interface (producer → consumer)
| | PSC endpoint | PSC interface |
|-|-------------|---------------|
| Direction | **Consumer → producer** (managed service *ingress*) | **Producer → consumer** (managed service *egress*) |
| Consumer-side resource | Endpoint (forwarding rule) | **Network attachment** (specifies the subnet to allocate from) |
| Producer-side resource | Service attachment | A VM NIC of type PSC interface (the VM's *other* NIC is in the producer VPC) |
| Transitive? | No | **Yes**: the producer can reach networks connected to the consumer VPC (on-prem via VPN/Interconnect, peers) |
| Use case | Consume a service | A managed service needs to **reach data inside the customer's network** |

When the consumer **accepts** the connection, Google allocates the interface and an IP from the network attachment's subnet. Producer and consumer can be in **different projects and organizations**. Producers document their own setup (API or UI).

### Service Connection Policies (service connectivity automation)
These let **consumer network admins** delegate endpoint creation safely to **consumer service admins**.
| Field | Meaning |
|-------|---------|
| **Service class** | Globally unique ID of a producer's managed service type |
| **VPC network** | Network the policy applies to |
| **Subnets** | Where PSC endpoint IPs are allocated |
| **Connection limit** | Max PSC connections the producer can create in that network + region |

**Flow:** (1) the network admin creates a **regional** policy (one service class × one VPC). (2) The service admin deploys the managed service via its API/UI. (3) The producer passes the connectivity config to a **service connection**. (4) **PSC automation creates the endpoint** in the consumer VPC, connected to the producer's service attachment.

Finding attachments: Google producers via UI / `describe`; self-hosted and third-party URLs are shared programmatically or by email.

### Caveats
- **Can't create an endpoint in the same VPC** as the published service. Endpoints are for *another* VPC.
- The endpoint IP **counts toward the project's global internal IP quota** (for Google API endpoints).
- **Endpoints aren't reachable from peered VPC networks**, so create an endpoint in the peered VPC too.
- **On-prem → non-Google (published) services:** the lecture says it must use **Cloud VPN**, in the **same region** as the endpoint. (Check current docs; Interconnect VLAN attachments are also supported for many PSC cases.)

---

## Key Terms

| Term | Definition |
|------|-----------|
| PSC endpoint | Consumer forwarding rule with an internal IP targeting a service attachment or Google APIs |
| PSC backend | PSC NEG used as a load balancer backend |
| Service attachment | Producer resource publishing a service to consumers |
| PSC NAT subnet | Producer range used to translate consumer traffic |
| Consumer accept list | Producer control over which projects/networks may connect |
| PSC interface | Producer-side NIC that lets a producer initiate traffic into a consumer VPC (transitive) |
| Network attachment | Consumer resource defining the subnet for PSC interfaces |
| Service connection policy | Regional policy allowing automated PSC endpoint deployment for a service class |
| Service class | Globally unique identifier of a producer's managed service |
| Multi-tenant producer | One producer VPC serving many consumer VPCs |

---

## Real-World Connections

**AWS equivalent: PrivateLink**
| GCP | AWS |
|-----|-----|
| PSC endpoint | **Interface VPC endpoint** |
| Service attachment | **VPC endpoint service** (backed by an NLB/GWLB) |
| Consumer accept list | Endpoint service **allowed principals** + acceptance |
| PSC for Google APIs | Interface endpoints for AWS service APIs |
| PSC interface | (No direct equivalent; closest is cross-account ENI / Lambda VPC access) |

**Network+ links:** PSC makes **zero trust** concrete ([[L08]]): no network-level reachability, only one service exposed at one IP. The producer firewall scoping to the NAT range is the **ACL** principle ([[L20]]). Overlapping IP tolerance is like **NAT** between networks ([[L09]]).

**k3s / Kubernetes:** PSC is like a Kubernetes **Service** that exposes one ClusterIP for a backend set, but across organizations. A SaaS vendor offering "private connectivity" (MongoDB Atlas, Confluent, Elastic Cloud on GCP) uses **PSC**. If you moved your Elasticsearch to Elastic Cloud, you'd create a PSC endpoint in your VPC to reach it privately.

---

## Troubleshooting Scenario

**Scenario:** Team A created a PSC endpoint `10.10.5.50` in VPC-A for a partner's API. Workloads in VPC-B, which is **peered** to VPC-A, time out connecting to `10.10.5.50`. VPC-A workloads work fine.

**Analysis:** PSC endpoints are **not accessible from peered VPC networks** (a documented caveat). The firewall and routes are fine.
**Fix:** Create a **second PSC endpoint in VPC-B** pointing at the same service attachment (the producer may need to accept VPC-B's project). Point VPC-B workloads at it, ideally via a Cloud DNS private zone name so both VPCs use the same hostname with different IPs.
**Verify:** `curl` from VPC-B to the new endpoint IP. Check the service attachment shows both connections **ACCEPTED**.

---

## Hands-On Ideas
1. **Producer/consumer lab:** two VPCs in one project with **overlapping** 10.0.0.0/24 subnets. In the producer, run an internal passthrough NLB → nginx VM, create a PSC NAT subnet and a service attachment. In the consumer, create an endpoint. `curl` works despite the overlap.
2. **PSC for Google APIs:** `gcloud compute addresses create psc-apis --global --purpose=PRIVATE_SERVICE_CONNECT --addresses=10.255.255.254 --network=VPC`, then a forwarding rule `--target-google-apis-bundle=all-apis`. Add a DNS record and check `dig storage.googleapis.com`.
3. Compare `gcloud compute service-attachments describe` output with the consumer endpoint status (`pscConnectionStatus`).

---

## Quiz

1. Which PSC resource does the **producer** create to publish a service?
   - A) Endpoint  B) Service attachment  C) Network attachment  D) Allocated range

2. Consumer and producer VPCs use overlapping 10.0.0.0/16 ranges. Which option still allows private connectivity?
   - A) VPC Network Peering  B) Private Services Access  C) Private Service Connect  D) Cloud VPN between them

3. Through a PSC endpoint, which side can initiate connections?
   - A) Producer only  B) Consumer only  C) Both  D) Neither; it's a broadcast domain

4. A managed service must reach a database inside the customer's VPC and an on-prem system connected to it. Which PSC feature fits?
   - A) PSC endpoint  B) PSC interface with a network attachment  C) PSC backend  D) Service connection policy

5. Which property distinguishes a PSC interface from a PSC endpoint?
   - A) The interface is transitive and producer-initiated  B) The interface needs VPC peering  C) The interface works only for Google APIs  D) The interface uses external IPs

6. A network admin wants service admins to deploy a managed service and have PSC endpoints created automatically, but only in specific subnets and with a cap. What should they configure?
   - A) A service connection policy  B) A Cloud NAT gateway  C) An allocated IP range  D) A firewall policy

7. Workloads in a peered VPC can't reach a PSC endpoint in the neighboring VPC. What's the fix?
   - A) Enable custom route export  B) Create a PSC endpoint in the peered VPC  C) Add Cloud NAT  D) Enable PGA

8. How must the producer's firewall rules be scoped for PSC consumer traffic?
   - A) To every consumer subnet  B) To the PSC NAT subnet range  C) To 0.0.0.0/0  D) No firewall rules apply

9. What's the AWS equivalent of PSC?
   - A) NAT Gateway  B) PrivateLink (interface endpoints / endpoint services)  C) Transit Gateway  D) Direct Connect

10. In which PSC configuration does the consumer connect using an external IP?
    - A) Internal ALB with a PSC NEG  B) Global external ALB with a PSC NEG  C) PSC endpoint for Google APIs  D) PSC interface

**Answers:** 1-B, 2-C, 3-B, 4-B, 5-A, 6-A, 7-B, 8-B, 9-B, 10-B

---

## Weak Areas to Revisit
*(none flagged yet)*

---

## Linked Pages
- [[GCP07]] — Private access options overview · [[GCP08]] — Private Google Access
- [[GCP10]] — Private Services Access (peering-based alternative)
- [[GCP11]] — Cloud NAT
- [[concepts/gcp-private-access]]
- [[certs/gcp-networking]]
