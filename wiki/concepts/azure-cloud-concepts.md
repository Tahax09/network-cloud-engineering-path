---
title: Azure Cloud Concepts (AZ-900 Domain 1)
type: concept
tags: [azure, az-900, cloud-concepts, benefits, capex-opex, deployment-models, shared-responsibility, iaas, paas, saas, serverless]
updated: 2026-10-22
---

# Azure Cloud Concepts (AZ-900 Domain 1)

Cloud definitions and traps for the first exam domain. The NIST view of the same ideas is in [[concepts/cloud-fundamentals]]. Exam page: [[certs/az-900]].

---

## 1. Benefits of cloud (the 8 words to tell apart)

| Benefit | Meaning | Keyword |
|---------|---------|---------|
| **High availability** | Service stays up with minimal downtime, backed by an SLA (e.g. 99.95%) | uptime / SLA |
| **Scalability** | Add resources to handle more load. *Vertical* = bigger VM (scale up). *Horizontal* = more VMs (scale out). | handle growth |
| **Elasticity** | Scale automatically out *and back in* as demand changes, so you pay only for what you need | automatic, up and down |
| **Reliability** | Recover from failure and keep working, using multiple regions and zones | resilient, self-healing |
| **Predictability** | Performance (autoscale, load balancing) and cost (forecasting, TCO tools) are foreseeable | forecast |
| **Security** | Built-in threat protection and compliance; you choose the level of control | protect, comply |
| **Governance** | Standards and policies enforced across resources (Azure Policy, templates) | compliance rules |
| **Manageability** | *Of* the cloud: autoscale, templates, monitoring. *In* the cloud: Portal, CLI, APIs. | manage resources |

NIST link: *rapid elasticity* = **elasticity**; *measured service* = **consumption-based** pricing.

---

## 2. CapEx vs OpEx, and consumption-based pricing

| | CapEx — capital expenditure | OpEx — operational expenditure |
|--|-----------------------------|--------------------------------|
| What | Upfront spend on physical assets (servers, datacentre) | Spend on services as you use them, billed per period |
| Paid | Once, depreciated over years | Recurring, no upfront cost |
| Typical of | On-premises | Cloud |

**Consumption-based model:** pay only for what you use, with no upfront cost, no termination fees, and billing stops when you stop using the resource. Cloud pricing is OpEx and consumption-based.

---

## 3. Cloud deployment models

| Model | Description | Trade-off |
|-------|-------------|-----------|
| **Public cloud** | Owned and run by a provider (Azure), shared hardware, accessed over the internet | No CapEx, high scalability; less control over the hardware |
| **Private cloud** | Used only by one organization, on-premises or hosted | Full control and custom security; you buy and maintain it (CapEx) |
| **Hybrid cloud** | Public + private connected together; keep sensitive data on-prem, burst to Azure for peaks | Azure Arc and ExpressRoute help here |

**Also know:** *multi-cloud* means more than one public provider (e.g. Azure + AWS), which is different from hybrid. **Azure Arc** manages resources across on-prem and other clouds.

---

## 4. Shared responsibility model

Customer / Microsoft / Shared, by service type:

| Responsibility | On-premises | IaaS | PaaS | SaaS |
|----------------|-------------|------|------|------|
| Data & information | Customer | Customer | Customer | Customer |
| Devices | Customer | Customer | Customer | Customer |
| Accounts & identities | Customer | Customer | Customer | Customer |
| Identity & directory infrastructure | Customer | Shared | Shared | Shared |
| Applications | Customer | Customer | Shared | **Microsoft** |
| Network controls | Customer | Customer | Shared | Shared |
| Operating system | Customer | Customer | **Microsoft** | **Microsoft** |
| Physical hosts | Customer | **Microsoft** | **Microsoft** | **Microsoft** |
| Physical network | Customer | **Microsoft** | **Microsoft** | **Microsoft** |
| Physical datacentre | Customer | **Microsoft** | **Microsoft** | **Microsoft** |

**How to read it:** the top three rows are customer-owned in every model, even SaaS. The bottom three rows belong to Microsoft in every cloud model. The middle shifts as you move from IaaS to PaaS to SaaS: the further right, the more Microsoft manages and the less control you have.

### Service types with Azure examples
| Type | What you rent | Azure example |
|------|---------------|---------------|
| **IaaS** | The infrastructure | Virtual Machines |
| **PaaS** | A platform to run your code | App Service, Azure SQL Database |
| **SaaS** | Finished software | Microsoft 365, Dynamics 365 |
| **Serverless** | Code that runs on events; Azure manages servers and scaling, you pay per execution | Azure Functions |

---

## 5. Exam traps for this domain
- **Scalability vs elasticity:** "automatically scale in and out" → elasticity.
- **High availability vs reliability:** uptime/SLA → availability; recover and keep working → reliability.
- **Vertical vs horizontal:** up/down = resize one VM; out/in = change the number of VMs.
- **Hybrid vs multi-cloud:** hybrid = public + private; multi-cloud = two or more public providers.
- **CapEx vs OpEx:** moving to cloud shifts spend from CapEx to OpEx. "No upfront cost" → OpEx and consumption-based.
- **Always customer-owned:** data, devices, accounts/identities, never transferred to Microsoft.
- **SLA percentages:** more nines = less allowed downtime. A **composite SLA** (an app depending on several services) is the *product* of the individual SLAs, so it is lower than any single one.

## Compared with AWS
The shared responsibility idea is identical (AWS: security *of* the cloud vs security *in* the cloud), and the IaaS/PaaS/SaaS split is the same. The vocabulary differs: Microsoft tests "scalability vs elasticity" and "CapEx vs OpEx" definitions explicitly.
