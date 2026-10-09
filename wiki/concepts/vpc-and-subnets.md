# VPC & Subnets

## What Is a VPC?
A Virtual Private Cloud is a logically isolated network within a cloud provider's infrastructure. You control the IP range, subnets, routing, and firewall rules. Other tenants cannot access your VPC.

**Analogy:** Renting a floor in a skyscraper — the building is shared, but your floor has its own locked doors and internal layout.

## VPC Architecture (AWS)

```
┌──────────────── VPC (10.0.0.0/16) ────────────────────┐
│                                                        │
│  ┌─── Public Subnet ───┐    ┌─── Private Subnet ───┐  │
│  │  10.0.1.0/24        │    │  10.0.2.0/24         │  │
│  │  Web Servers, ALB   │    │  Databases, App Svrs │  │
│  └──────────┬──────────┘    └──────────┬────────────┘  │
│      [Internet Gateway]          [NAT Gateway]         │
└─────────────┼───────────────────────────┼───────────────┘
              │                           │
          INTERNET                   INTERNET
       (inbound + outbound)      (outbound only — no inbound)
```

## Key Components

### Internet Gateway (IGW)
- Attached to VPC; enables public internet access for public subnets
- Public subnet route table: `0.0.0.0/0 → igw-xxxx`

### NAT Gateway
- Lives in a **public** subnet
- Allows private subnet instances to reach internet (outbound only)
- Private subnet route table: `0.0.0.0/0 → nat-xxxx`
- Inbound connections from internet are blocked

### Route Table
- Every subnet has an associated route table
- Controls where traffic is sent based on destination CIDR
- Most specific route wins

### Security Groups (Stateful, per-instance)
- Applied to EC2 instances / ENIs
- Stateful: allow inbound → return traffic automatically allowed
- Default: deny all inbound, allow all outbound
- Rules based on: Protocol, Port, Source IP/CIDR/SG

### Network ACLs (Stateless, per-subnet)
- Applied to subnets
- Stateless: must explicitly allow BOTH directions (inbound AND outbound)
- Rules processed in numbered order; first match wins
- Default: allow all (can be customized)

## Security Group vs Network ACL

| | Security Group | Network ACL |
|-|---------------|-------------|
| Level | Instance | Subnet |
| Stateful | Yes | No |
| Rule evaluation | All rules evaluated | First match wins |
| Default behavior | Deny all inbound | Allow all |
| Analogy | Personal bodyguard | Neighborhood checkpoint |

## Public vs Private Subnet Decision

| Question | Public Subnet | Private Subnet |
|----------|--------------|----------------|
| Needs inbound from internet? | Yes | No |
| Has IGW route? | Yes | No |
| Gets public IP? | Yes (optional) | No |
| Examples | Web servers, LBs, Bastion hosts | Databases, app servers |

## Cloud Connectivity

| Option | Path | Security | Use Case |
|--------|------|----------|---------|
| Public Internet | ISP → Internet → Cloud | TLS required | Dev/test |
| Site-to-Site VPN | IPsec tunnel over internet | Encrypted | Business standard |
| Direct Connect | Dedicated private circuit | Private (not internet) | Enterprise, compliance |

## Five-Tuple Firewall Rule
All cloud firewall rules (SG, ACL, NGFW) are built on:
1. Source IP
2. Source Port
3. Destination IP
4. Destination Port
5. Protocol (TCP/UDP/ICMP)

## k3s Private Cloud Mapping

| k3s Concept | AWS VPC Equivalent |
|-------------|-------------------|
| Pod CIDR (10.42.0.0/16) | VPC CIDR |
| Node network interface | Subnet |
| CNI routes | Route Table |
| kube-proxy iptables NAT | NAT Gateway |
| iptables rules | Security Group |
| Ingress Controller | Internet Gateway + ALB |

## Linked Lessons
- [[L03 - Network Virtualization & Cloud Technologies]] — full lesson on cloud networking
- [[concepts/cloud-fundamentals]] — service models and deployment types
- [[labs/k3s-lab]] — k3s as private cloud
