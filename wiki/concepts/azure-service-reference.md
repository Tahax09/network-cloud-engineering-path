---
title: Azure Service Reference (what, when, pros and cons)
type: concept
tags: [azure, az-900, services, pros-cons, compute, networking, storage, database, identity, governance, reference]
updated: 2026-10-22
---

# Azure Service Reference (what, when, pros and cons)

Each row gives the service, what it does, when to use it, and the pros and cons. The cons are the part exam questions usually probe. Category overview with AWS/GCP equivalents: [[concepts/azure-product-map]]. Exam page: [[certs/az-900]].

**How to use it in an answer:** pick the row that matches the scenario keyword, then write the pro that serves the requirement and the con that matters in this context, always finishing the sentence with *"because [scenario requirement]"*. Example: "Application Gateway fits because the scenario needs HTTP load balancing with health checks; the trade-off is a higher cost than Load Balancer and a dedicated subnet."

---

## Compute

| Service | What it does | When to use | Pros | Cons |
|---------|--------------|-------------|------|------|
| **Virtual Machines** | IaaS virtual servers you fully control | Custom OS or software, lift-and-shift | Full control, runs anything | You patch the OS; pay while running; manual scaling |
| **VM Scale Sets** | Group of identical VMs that scales as one | Web tier with variable load | Autoscale, zone spread, identical instances | Still manage OS images and config |
| **App Service** | PaaS hosting for web apps and APIs | Web app, minimal management | No OS patching, built-in autoscale, deployment slots | Less control; plan billed even when idle |
| **Functions** | Serverless code triggered by events | Short event-driven tasks | Pay per execution, no servers, auto scale | Execution time limits, cold starts, stateless design |
| **AKS** | Managed Kubernetes for containers | Microservices at scale | Orchestration, portability, rolling updates | Complex; you manage node pools and upgrades |
| **Container Instances** | Run a container with no orchestrator | Quick, simple container jobs | Starts in seconds, per-second billing | Limited scaling and features |

## Networking

| Service | What it does | When to use | Pros | Cons |
|---------|--------------|-------------|------|------|
| **Virtual Network** | Your private network in Azure with subnets | Always, as the base of any solution | Isolation, own IP ranges, hybrid connect | Address space must be planned; overlaps are painful to fix |
| **NSG** | Stateful allow/deny rules on subnet or NIC | Control which ports and sources reach a tier | Free, simple, priority-based rules | Layer 3/4 only; priority mistakes block traffic |
| **Load Balancer** | Layer-4 TCP/UDP distribution | Non-HTTP traffic, high throughput | Very fast, zone-redundant, low cost | No URL routing, WAF or SSL offload |
| **Application Gateway** | Layer-7 HTTP load balancer with WAF option | Web apps needing URL routing or WAF | SSL termination, WAF, autoscale, health probes | Costs more than Load Balancer; regional; needs own subnet |
| **Front Door** | Global HTTP entry point with CDN and WAF | Multi-region or global users | Global failover, edge caching, WAF | Overkill and extra cost for single-region apps |
| **VPN Gateway** | Encrypted tunnel to on-premises | Cheap, quick hybrid connectivity | Low cost, fast to set up, encrypted | Goes over the internet: variable latency, bandwidth limits |
| **ExpressRoute** | Private dedicated link to on-premises | High, steady bandwidth; compliance | Private, predictable latency, high throughput | Expensive, provider lead time, not encrypted by default |
| **Private Endpoint** | Private IP in your VNet for a PaaS service | Keep SQL, Storage, Key Vault off the internet | Removes public exposure, limits data exfiltration | Needs private DNS; hourly and data charges |

## Storage and databases

| Service | What it does | When to use | Pros | Cons |
|---------|--------------|-------------|------|------|
| **Blob Storage** | Object storage with Hot/Cool/Cold/Archive tiers | Images, backups, logs, static files | Cheap, durable, massively scalable | Not a file system; Archive retrieval can take hours |
| **Azure Files** | Managed SMB/NFS file shares | Lift-and-shift apps needing a file share | Drop-in replacement for file servers | Costlier than Blob; performance tied to tier |
| **Managed Disks** | Block storage attached to VMs | OS and data disks for VMs | Durable, snapshots, simple to attach | Billed for provisioned size, tied to VMs |
| **Azure SQL Database** | Managed SQL Server database (PaaS) | Relational app data, minimal admin | Automatic backups, patching, built-in HA | Not 100% SQL Server compatible; high tiers cost more |
| **SQL Managed Instance** | Managed SQL with near-full SQL Server features | Migrating existing SQL Server apps | Near-100% compatibility | Pricier, slower to provision |
| **Cosmos DB** | Globally distributed multi-model NoSQL | Low-latency global apps, flexible schema | Single-digit ms latency, multi-region writes, SLAs | Request-unit pricing can surprise; needs different data modelling |
| **Cache for Redis** | Managed in-memory cache | Speed up reads, store sessions | Sub-millisecond reads, lowers DB load | Extra cost; you handle cache invalidation |

## Identity and security

| Service | What it does | When to use | Pros | Cons |
|---------|--------------|-------------|------|------|
| **Microsoft Entra ID** | Cloud identity and directory | User sign-in, SSO, MFA | Central identity, Conditional Access | Advanced features need paid licences |
| **RBAC** | Roles assigned to identities at a scope | Control who can do what on resources | Fine-grained, inherited down the hierarchy | Easy to over-grant (Owner/Contributor) |
| **Managed Identity** | Credential-free identity for an Azure resource | VM or app accessing Key Vault, SQL, Storage | No passwords to store or rotate | Azure resources only; still needs role assignments |
| **Key Vault** | Store secrets, keys and certificates | Any password, connection string or key | Central, audited, supports rotation | Extra dependency; throttling; wrong RBAC blocks the app |
| **Defender for Cloud** | Security posture and threat protection | Continuous security assessment | Security score, recommendations, alerts | Advanced plans are paid per resource |

## Management and governance

| Service | What it does | When to use | Pros | Cons |
|---------|--------------|-------------|------|------|
| **Azure Monitor** | Metrics, logs and alerts in one place | Monitoring, autoscale triggers, alerting | Unified view, powers autoscale | Log ingestion costs; noisy if alerts untuned |
| **Azure Policy** | Enforce or audit rules on resources | Standards, e.g. allowed regions or required tags | Governance at scale, audit and deny modes | Can block deployments unexpectedly |
| **Resource Locks** | Block deletion or modification of a resource | Protect critical resources | Prevents accidental deletion | Can block legitimate changes until removed |
| **Cost Management** | Cost analysis, budgets and alerts | Track and cap spending | Budgets and forecasts per scope | Alerts warn but do not stop spending |
| **Azure Advisor** | Free recommendations on 5 pillars | Periodic optimisation review | Free, covers cost, security, reliability | Advice only; does not apply fixes |
| **ARM / Bicep** | Infrastructure as code templates | Repeatable, consistent deployments | Repeatable, versionable, idempotent | Learning curve; Azure-only |

---

## Three rows people mix up most
- **Load Balancer vs Application Gateway:** layer 4 vs layer 7. If the scenario mentions HTTP, URL paths or WAF, it is Application Gateway.
- **VPN Gateway vs ExpressRoute:** encrypted over the internet vs private dedicated line. Cost-sensitive points to VPN; steady high bandwidth or compliance points to ExpressRoute.
- **SQL Database vs SQL Managed Instance:** "new cloud app" points to SQL Database; "migrate an existing SQL Server with minimal changes" points to Managed Instance.

Related: [[concepts/azure-scenario-framework]] (decision rules for picking between these) · [[AZS01 - Contoso Clinic Highly Available Web App]]
