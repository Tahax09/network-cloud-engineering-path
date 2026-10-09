# Cloud Fundamentals

## 5 NIST Cloud Characteristics

| Characteristic | Meaning | Exam Note |
|---------------|---------|-----------|
| On-Demand Self-Service | Provision without human interaction | About provisioning |
| Measured Service | Pay-as-you-go billing | About billing |
| Rapid Elasticity | Scale up AND down dynamically | "Handle traffic spikes" → this one |
| Resource Pooling | Multi-tenant shared hardware, logically isolated | |
| Broad Network Access | Available anywhere via standard connections | |

## Service Models

### IaaS — Infrastructure as a Service
- You manage: App, Data, Runtime, Middleware, **OS**
- Provider manages: Virtualization, Servers, Storage, Networking
- **AWS:** EC2, VPC, EBS
- Use: Full OS control, lift-and-shift migrations

### PaaS — Platform as a Service
- You manage: App, **Data**
- Provider manages: Runtime, Middleware, OS, and below
- **AWS:** Elastic Beanstalk, RDS, Lambda (partially)
- Use: Deploy code without managing servers

### SaaS — Software as a Service
- You: Use the software
- Provider manages: Everything
- Examples: Gmail, Slack, Salesforce, Microsoft 365
- Use: Business tools, no infrastructure involvement

## Deployment Models

| Model | Who Has Access | Physical Ownership | Example |
|-------|---------------|-------------------|---------|
| Public | Anyone | Provider | AWS, GCP, Azure |
| Private | One org | You (or managed for you) | OpenStack on-prem |
| Hybrid | Controlled mix | Both | On-prem DB + cloud web tier |
| Community | Industry group | Shared | AWS GovCloud, EDU clouds |

## Hypervisor Types

| | Type 1 | Type 2 | Container |
|-|--------|--------|-----------|
| Runs on | Hardware | Host OS | Host OS |
| Examples | ESXi, KVM | VirtualBox, VMware Workstation | Docker, containerd |
| Cloud use | AWS EC2 (Nitro) | Dev workstations | k3s, EKS pods |
| Kernel sharing | No | No | Yes (host kernel shared) |

## SDN — Software-Defined Networking
- Separates **control plane** (routing decisions) from **data plane** (packet forwarding)
- Central software controller programs forwarding rules on commodity hardware
- **AWS:** Route Tables, Security Groups, VPC — all SDN under the hood

## NFV — Network Function Virtualization
- Run firewall, router, LB as **software** on general-purpose servers
- No dedicated physical appliances
- **AWS Security Group** = virtual firewall; no physical box exists for your VPC

## AWS Service Mapping

| Concept | AWS Service |
|---------|-------------|
| IaaS Compute | EC2 |
| IaaS Network | VPC, Subnets, Route Tables |
| IaaS Storage | EBS, S3 |
| PaaS Compute | Elastic Beanstalk, Lambda |
| PaaS Database | RDS, DynamoDB |
| CDN | CloudFront |
| DNS | Route 53 |
| Load Balancer | ALB, NLB, GLB |
| Direct Connect | AWS Direct Connect |
| Site-to-Site VPN | AWS VPN + Virtual Private Gateway |

## Linked Lessons
- [[L03 - Network Virtualization & Cloud Technologies]] — full lesson
- [[concepts/vpc-and-subnets]] — VPC design detail
