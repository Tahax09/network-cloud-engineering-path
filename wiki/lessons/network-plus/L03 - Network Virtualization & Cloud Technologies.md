---
id: L03
aliases: [L03]
title: Network Virtualization & Cloud Technologies
course: CompTIA Network+
cert_track: network-plus
objective: "1.0 — Networking Concepts"
date: 2025-05-28
status: Complete
quiz_attempted: No
weak_areas: []
tags: [cloud, iaas, paas, saas, vpc, virtualization, sdn, nfv, nat, hypervisor, security-groups]
---

# L03 — Network Virtualization & Cloud Technologies

## Summary
Cloud = virtualization at massive scale with a billing interface on top. This lesson explains what makes cloud different from just renting a server: the 5 characteristics, 3 service models (IaaS/PaaS/SaaS), 4 deployment models, and the networking technologies underneath — VPCs, SDN, NFV, NAT, and connectivity options. Everything here maps directly to AWS services.

## Core Concepts

### Hypervisor Types — The Foundation

| | Type 1 (Bare Metal) | Type 2 (Hosted) | Container |
|-|--------------------|-----------------|-----------|
| Runs on | Hardware directly | Host OS | Host OS |
| Examples | ESXi, Hyper-V, KVM | VirtualBox, VMware Workstation | Docker, containerd |
| Used by | AWS/GCP/Azure EC2 | Developer workstations | k3s pods |
| Isolation | Full VM isolation | Full VM isolation | Shared kernel |
| Performance | Highest | Lower (host OS overhead) | Lightest |

**Your stack:** Containers (containerd via k3s) on top of a Type 2 VM. AWS EC2 uses a custom Type 1 hypervisor (Nitro).

### 5 Cloud Characteristics (NIST)

| Characteristic | Meaning |
|---------------|---------|
| On-Demand Self-Service | Provision resources yourself; no IT phone call |
| Measured Service | Pay-as-you-go; billed for what you consume |
| Rapid Elasticity | Grow AND shrink capacity with demand — Auto Scaling |
| Resource Pooling | Multi-tenant shared hardware; logical isolation |
| Broad Network Access | Accessible anywhere via standard connections (API, CLI, web) |

**Exam trap:** "Handles Black Friday traffic spikes" → **Rapid Elasticity** (not On-Demand; that's about provisioning, not scaling).

### Service Models — The Responsibility Stack

```
                  YOU MANAGE          |    PROVIDER MANAGES
══════════════════════════════════════════════════════════
On-Premise:  Applications, Data, Runtime, Middleware,
             OS, Virtualization, Servers, Storage, Networking

IaaS:        Applications ✅           Virtualization ☁️
             Data ✅                   Servers ☁️
             Runtime ✅                Storage ☁️
             Middleware ✅             Networking ☁️
             OS ✅  ← your line
             AWS: EC2, VPC, EBS

PaaS:        Applications ✅           Runtime ☁️
             Data ✅  ← your line      Middleware ☁️
                                       OS + below ☁️
             AWS: Elastic Beanstalk, RDS, Lambda

SaaS:        Use the software ✅       EVERYTHING ☁️
             Examples: Gmail, Slack, Salesforce
```

**Analogy:**
- IaaS = Empty apartment (you furnish and clean)
- PaaS = Furnished apartment (you bring your stuff)
- SaaS = Hotel room (just show up)

### Deployment Models

| Model | Tenancy | Access | Cost | Examples |
|-------|---------|--------|------|---------|
| Public | Multi-tenant | Anyone | Pay-as-you-go | AWS, GCP, Azure |
| Private | Single-tenant | Org only | High CapEx | OpenStack on own servers |
| Hybrid | Mixed | Controlled | Mixed | On-prem DB + cloud web servers |
| Community | Group/industry | Consortium only | Shared | AWS GovCloud, EDU clouds |

**Don't forget Community** — often missed on exams. Shared compliance requirements, shared cost within an industry.

### VPC — Virtual Private Cloud

```
┌──────────────── VPC (10.0.0.0/16) ────────────────────┐
│                                                        │
│  ┌─── Public Subnet ───┐    ┌─── Private Subnet ───┐  │
│  │  10.0.1.0/24        │    │  10.0.2.0/24         │  │
│  │  Web Servers, LB    │    │  Databases, App Svrs │  │
│  └──────────┬──────────┘    └──────────┬────────────┘  │
│      [Internet Gateway]          [NAT Gateway]         │
└─────────────┼───────────────────────────┼───────────────┘
              │                           │
          INTERNET                   INTERNET
       (public inbound/out)      (private → public only)
```

VPC enforces isolation via: Subnets, Route Tables, Security Groups, Network ACLs.

**Security Groups vs Network ACLs:**

| | Security Group | Network ACL |
|-|---------------|-------------|
| Applied to | Instance / ENI | Subnet |
| Stateful | Yes (return auto-allowed) | No (need both directions) |
| Analogy | Personal bodyguard | Neighborhood checkpoint |

### SDN & NFV — Software-Defined Everything

**Traditional:** Physical router has forwarding hardware + routing logic baked in together. Vendor-locked, slow to change.

**SDN (Software-Defined Networking):**
- Intelligence moved to a centralized software controller
- Commodity hardware just does the forwarding
- Controller programs rules in real time

**NFV (Network Function Virtualization):**
- Run firewall, router, LB as software on general-purpose servers
- No dedicated physical appliance needed

**AWS reality:** When you configure a Security Group, Route Table, or Internet Gateway — that IS SDN/NFV. No physical firewall box exists for your VPC.

**k3s reality:** kube-proxy + iptables = SDN. CNI plugin programs virtual interfaces and routing rules in software.

### NAT — Network Address Translation
Private RFC 1918 addresses (10.x, 172.16.x, 192.168.x) are NOT routable on the public internet.

```
Private PC (192.168.1.5) → [NAT Device] → rewrites source to public IP → Internet
Return traffic arrives → NAT rewrites destination back → 192.168.1.5
```

NAT table tracks the mapping so return traffic reaches the right internal host.
- **AWS NAT Gateway:** Lets private subnet instances reach internet without being directly reachable from internet.

### Cloud Connectivity Options

| Option | Security | Reliability | Cost | Use Case |
|--------|----------|-------------|------|----------|
| Public Internet | Must use TLS | Best-effort | Lowest | Dev/test, small biz |
| Site-to-Site VPN | IPsec encrypted | Internet-dependent | Low-Medium | Most business use |
| Direct Connect (AWS) | Private circuit | SLA-backed | High | Enterprise, compliance |
| Cloud Gateway | Virtual edge devices | Varies | Varies | Multi-cloud |

**Decision:** Small/test → HTTPS. Business-critical → VPN. Enterprise/compliance → Direct Connect.

### Five-Tuple (Firewall Rule Foundation)
Every cloud firewall rule is built on:
1. Source IP
2. Source Port
3. Destination IP
4. Destination Port
5. Protocol (TCP/UDP/ICMP)

## Key Terms

| Term | Definition |
|------|------------|
| Hypervisor | Software layer that creates and manages VMs |
| Type 1 | Bare-metal hypervisor, runs directly on hardware |
| Type 2 | Hosted hypervisor, runs on a host OS |
| IaaS | You manage OS up; provider manages hardware/virtualization |
| PaaS | You manage app/data; provider manages platform |
| SaaS | Provider manages everything; you just use the software |
| VPC | Virtual Private Cloud — logically isolated network in cloud |
| Public Subnet | Subnet with route to Internet Gateway; directly reachable |
| Private Subnet | No direct internet route; uses NAT for outbound only |
| NAT Gateway | Allows private instances to reach internet; blocks inbound |
| Security Group | Stateful per-instance firewall in AWS |
| Network ACL | Stateless per-subnet firewall in AWS |
| SDN | Software-Defined Networking — control plane separated from data plane |
| NFV | Network Function Virtualization — network functions as software |
| Direct Connect | AWS dedicated private circuit from on-prem to cloud |
| Five-Tuple | Source IP, Source Port, Dest IP, Dest Port, Protocol |
| Community Cloud | Shared cloud for specific industry/group |

## Real-World Connections

**AWS VPC = Physical Network, Virtualized:**
- Subnets = physical network segments
- Route Tables = router configs
- Security Groups = per-host stateful firewalls
- Network ACLs = subnet-level packet filters
- Internet Gateway = edge router to internet
- NAT Gateway = outbound-only internet access for private resources

**Your k3s cluster = private cloud:**
- Pods get private IPs (not internet-routable)
- kube-proxy does NAT for service IPs
- CNI handles L3 routing between nodes
- Ingress controller = edge router + reverse proxy

## Troubleshooting Scenario
> "EC2 instance in private subnet can't reach the internet to download updates."

1. Private subnet → should have a route to a NAT Gateway
2. Check route table: is there `0.0.0.0/0 → nat-xxxx`?
3. NAT Gateway exists and is in a **public** subnet? (NAT Gateway must be in a public subnet with IGW route)
4. Security Group: does it allow outbound traffic? (SGS are stateful but outbound is usually open by default)
5. Network ACL: stateless — need explicit outbound AND return traffic rules

## Hands-On Practice
```bash
# Your k3s node's pod routing (CNI-managed routes)
ip route show

# See NAT rules — kube-proxy creates these for service IPs
sudo iptables -t nat -L -n -v

# See your pod network interfaces (virtual NICs)
ip addr show

# Check what your cluster is using for CNI
kubectl get pods -n kube-system
# Look for flannel, calico, or weave pod
```

## Quiz Questions
1. Which cloud characteristic allows a company to handle Black Friday traffic spikes?
   a) On-Demand Self-Service b) Resource Pooling **c) Rapid Elasticity** d) Broad Network Access

2. A developer wants to deploy their app without managing OS patching. Which model?
   **PaaS** — provider manages runtime/OS; developer manages app/data

3. A hospital consortium shares a cloud environment for HIPAA compliance. Which deployment model?
   **Community Cloud**

4. An EC2 instance in a private subnet can ping google.com. What is required for this?
   NAT Gateway in a public subnet + route `0.0.0.0/0 → nat-xxxx` in the private subnet's route table

5. AWS Security Groups are stateful. AWS Network ACLs are stateless. What does stateless mean in practice?
   You must explicitly allow BOTH inbound AND outbound directions; no automatic return traffic allowance.

6. In your k3s cluster, kube-proxy uses iptables to rewrite service IP destinations to pod IPs. Which concept from this lesson describes this?
   **NAT** (Network Address Translation at the software level)

## Weak Areas to Revisit
*(none flagged yet)*

## Related
- [[L01 - The OSI Model]] — OSI layers underlying all cloud networking
- [[L02 - Networking Appliances & Applications]] — physical devices that cloud virtualizes
- [[concepts/cloud-fundamentals]] — IaaS/PaaS/SaaS deep reference
- [[concepts/vpc-and-subnets]] — VPC design and subnetting
- [[labs/k3s-lab]] — private cloud lab mapping
