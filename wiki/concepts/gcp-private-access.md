# GCP Private Access Options

**Definition:** Ways for workloads using **internal IPs only** (VMs, serverless, on-prem hosts) to reach Google APIs, managed services, partner services, and the internet without public IPs on the workloads. All options are **independent** and can be combined.

## Comparison

| Option | Source → Destination | Mechanism | Key facts |
|--------|---------------------|-----------|-----------|
| **Private Google Access** | Internal-only VM → Google APIs | **Per-subnet** toggle; route to API ranges via `default-internet-gateway` (stays on Google network) | No effect on VMs with external IPs; Google APIs only |
| **PGA for on-prem** | On-prem → Google APIs | VPN/Interconnect + DNS to `private` (199.36.153.8/30) or `restricted` (199.36.153.4/30) + Cloud Router **custom advertisement** + VPC static route | `restricted` = VPC-SC-supported APIs only; Cloud DNS can't do partial overrides (use BIND) |
| **Private Service Connect** | Consumer VPC → Google APIs or producer service | **Endpoint** (internal IP forwarding rule) → **service attachment** | No peering; overlapping IPs OK; consumer-initiated only |
| **Private Services Access** | VPC → Google-managed producer VPC (dedicated to you) | **VPC Network Peering** + **allocated range** | Cloud SQL private IP, Memorystore, Filestore; peering is non-transitive |
| **Serverless VPC Access** | Cloud Run / Functions / App Engine → VPC | Connector (/28) or Direct VPC egress | Lets serverless reach internal IPs |
| **Cloud NAT** | Internal-only VM → internet (outbound only) | Managed NAT on Cloud Router (PAT) | Not for inbound; for non-Google destinations |

## AWS Equivalents
| GCP | AWS |
|-----|-----|
| Private Google Access | Gateway VPC endpoints (S3/DynamoDB) + interface endpoints |
| Private Service Connect | PrivateLink (interface endpoints / endpoint services) |
| Private Services Access | (No direct equivalent; managed DBs live in your subnets) |
| Serverless VPC Access | Lambda in VPC |
| Cloud NAT | NAT Gateway |

## CLI
```bash
# Enable PGA on a subnet
gcloud compute networks subnets update SUBNET --region=REGION --enable-private-ip-google-access
gcloud compute networks subnets describe SUBNET --region=REGION --format="get(privateIpGoogleAccess)"

# Route for private.googleapis.com VIPs (needed if default route removed)
gcloud compute routes create pga-private --network=VPC \
  --destination-range=199.36.153.8/30 --next-hop-gateway=default-internet-gateway

# Advertise restricted VIPs to on-prem
gcloud compute routers update ROUTER --region=REGION --advertisement-mode=CUSTOM \
  --set-advertisement-groups=ALL_SUBNETS --set-advertisement-ranges=199.36.153.4/30
```

## Linked Lessons
- [[GCP07]] — Overview of private connection options
- [[GCP08]] — Private Google Access (cloud + on-prem)
- [[GCP01]] — Routing / next hops · [[GCP04]] — Cloud DNS private zones
- [[L09]] — NAT/PAT, BGP · [[L20]] — Hardening / attack surface
