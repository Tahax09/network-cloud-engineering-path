---
title: Google Cloud Product Map (with AWS equivalents)
type: concept
tags: [gcp, aws, azure, product-map, compute, storage, database, networking, security, reference]
updated: 2026-10-15
---

# Google Cloud Product Map (with AWS equivalents)

A quick reference: each Google Cloud product, what it does, and its closest AWS (and sometimes Azure) counterpart. ⭐ = core to the **Networking in Google Cloud** program.

> Equivalents are approximate. Scope and design differ, especially in networking: a GCP VPC is **global**, while an AWS VPC is **regional**.

---

## Compute
| Google Cloud | Used for | AWS | Azure |
|--------------|----------|-----|-------|
| **Compute Engine** ⭐ | VMs (IaaS); custom machine types, Spot VMs, MIGs for autoscaling | EC2 (+ Auto Scaling groups) | Virtual Machines / VMSS |
| **Google Kubernetes Engine (GKE)** ⭐ | Managed Kubernetes (Standard or Autopilot) | EKS | AKS |
| **Cloud Run** | Serverless containers: deploy an image, scale to zero, pay per request/CPU-second | Fargate / App Runner (closest to ECS-on-Fargate) | Container Apps |
| **Cloud Run functions** (formerly Cloud Functions) | Event-driven functions (FaaS) | Lambda | Functions |
| **App Engine** | PaaS for web apps (Standard / Flexible) | Elastic Beanstalk | App Service |
| **Batch** | Managed batch jobs on VMs | AWS Batch | Azure Batch |
| **Google Cloud VMware Engine** | Run VMware workloads natively in GCP | VMware Cloud on AWS | Azure VMware Solution |
| **Bare Metal Solution** | Dedicated hardware (e.g., Oracle) near GCP regions | EC2 bare-metal instances | BareMetal Infrastructure |

> **ECS?** GCP has no ECS-style proprietary orchestrator. Use **Cloud Run** for simple containers and **GKE** for Kubernetes.

## Storage
| Google Cloud | Used for | AWS | Azure |
|--------------|----------|-----|-------|
| **Cloud Storage** | Object storage (buckets); classes Standard / Nearline (30-day) / Coldline (90-day) / Archive (365-day minimum) | S3 (Standard / IA / Glacier) | Blob Storage |
| **Persistent Disk / Hyperdisk** | Block storage for VMs (zonal or regional replicated) | EBS | Managed Disks |
| **Local SSD** | Ephemeral, very fast disk attached to the host | Instance store | Temp disk |
| **Filestore** | Managed NFS file shares | EFS | Azure Files (NFS) |
| **Google Cloud NetApp Volumes** | Enterprise NFS/SMB (NetApp ONTAP) | FSx for NetApp ONTAP | Azure NetApp Files |
| **Parallelstore** | High-performance parallel file system for HPC/AI | FSx for Lustre | Managed Lustre |
| **Backup and DR Service** | Centralized backup | AWS Backup | Azure Backup |

## Databases and analytics
| Google Cloud | Used for | AWS | Azure |
|--------------|----------|-----|-------|
| **Cloud SQL** | Managed MySQL / PostgreSQL / SQL Server | RDS | Azure SQL / Database for PostgreSQL |
| **AlloyDB for PostgreSQL** | High-performance PostgreSQL-compatible | Aurora PostgreSQL | Cosmos DB for PostgreSQL (roughly) |
| **Spanner** | Globally distributed, strongly consistent relational DB | Aurora DSQL (closest); otherwise none | Cosmos DB (roughly) |
| **Firestore** | Serverless NoSQL document DB (mobile/web) | DynamoDB / DocumentDB | Cosmos DB |
| **Bigtable** | Wide-column NoSQL for huge throughput (time series, IoT) | DynamoDB / Keyspaces | Cosmos DB (Cassandra API) |
| **Memorystore** | Managed Redis / Valkey / Memcached | ElastiCache / MemoryDB | Azure Cache for Redis |
| **BigQuery** | Serverless data warehouse; SQL over petabytes | Redshift + Athena | Synapse / Fabric |
| **Dataflow** | Stream + batch processing (Apache Beam) | Kinesis Data Analytics (Managed Flink) / Glue | Stream Analytics |
| **Dataproc** | Managed Spark / Hadoop | EMR | HDInsight |
| **Pub/Sub** | Global messaging / event ingestion | SNS + SQS / Kinesis Data Streams | Service Bus / Event Hubs |
| **Cloud Composer** | Managed Apache Airflow | MWAA | Data Factory (Airflow) |
| **Looker / Looker Studio** | BI and dashboards | QuickSight | Power BI |
| **Dataplex** | Data governance / catalog | Lake Formation / DataZone | Purview |
| **Datastream** | Change data capture (CDC) replication | DMS (CDC) | — |

## Networking ⭐ (this program)
| Google Cloud | Used for | AWS | Lessons |
|--------------|----------|-----|---------|
| **VPC network** | **Global** private network; regional subnets; routes; firewall rules | VPC (regional) | [[GCP15 - Cloud Network Architecture Overview]] |
| **Shared VPC / VPC Network Peering** | Centralized network for many projects / connect two VPCs (non-transitive) | Resource Access Manager (VPC sharing) / VPC peering | [[GCP18 - Hub-and-Spoke Topology]] |
| **Cloud Load Balancing** | Global/regional, L4/L7, external/internal; single anycast IP | ELB (ALB / NLB / GWLB) + Global Accelerator | Course 5 |
| **Cloud CDN / Media CDN** | Edge caching | CloudFront | [[GCP26 - Google Cloud DDoS Mitigations]] |
| **Cloud DNS** | Managed authoritative and private DNS | Route 53 | [[GCP04 - Cloud DNS]] |
| **Cloud NAT** | Managed outbound NAT (no external IPs on VMs) | NAT Gateway | [[GCP11 - Cloud NAT]] |
| **Cloud Router** | Managed BGP speaker for VPN/Interconnect/NCC | (BGP inside VGW / TGW / DX) | [[GCP11 - Cloud NAT]] |
| **Cloud VPN (HA VPN)** | IPsec to on-prem or other clouds, 99.99% SLA | Site-to-Site VPN | Course 6 |
| **Cloud Interconnect** (Dedicated / Partner / Cross-Cloud) | Private high-bandwidth links to on-prem or other clouds | Direct Connect | Course 6 |
| **Network Connectivity Center (NCC)** | Hub-and-spoke transit for VPCs and hybrid | Transit Gateway / Cloud WAN | [[GCP18 - Hub-and-Spoke Topology]] |
| **Private Service Connect** | Private endpoints to Google APIs or producer services | PrivateLink (interface endpoints) | [[GCP09 - Private Service Connect]] |
| **Private Google Access** | Reach Google APIs from VMs without external IPs | Gateway / interface VPC endpoints | [[GCP08 - Private Google Access]] |
| **Private services access** | Peering to Google-managed services (Cloud SQL, etc.) | (RDS in your VPC) | [[GCP10 - Private Services Access]] |
| **Cloud Armor** | WAF + DDoS protection at the edge | AWS WAF + Shield | [[GCP26 - Google Cloud DDoS Mitigations]] |
| **Cloud NGFW** | Distributed stateful firewall; Enterprise tier adds IPS | Network Firewall | Course 4 |
| **Cloud IDS** | Managed intrusion detection (Palo Alto engine) on mirrored traffic | Traffic Mirroring + GuardDuty / partner | Course 4 |
| **Packet Mirroring** | Copy VM traffic to collectors | VPC Traffic Mirroring | Course 4 |
| **Secure Web Proxy** | Managed egress web proxy | Network Firewall (domain filtering) | — |
| **Network Intelligence Center** | Topology, Connectivity Tests, Performance Dashboard, Firewall Insights | Reachability Analyzer / Network Manager | [[GCP21 - Getting Topology Data]] |
| **Network Service Tiers** | Premium (Google backbone) vs Standard (public internet) routing | (no direct equivalent; Global Accelerator is closest) | [[GCP15 - Cloud Network Architecture Overview]] |
| **VPC Flow Logs** | Flow sampling per subnet | VPC Flow Logs | — |

## Security and identity
| Google Cloud | Used for | AWS | Azure |
|--------------|----------|-----|-------|
| **IAM** | Roles and permissions on the resource hierarchy | IAM | Azure RBAC |
| **Cloud Identity** | Users, groups, SSO | IAM Identity Center | Entra ID |
| **Identity-Aware Proxy (IAP)** | Zero-trust access to apps and SSH/RDP without a VPN or public IP | Verified Access / SSM Session Manager | Entra App Proxy / Bastion |
| **Cloud KMS / Cloud HSM / Cloud EKM** | Key management / HSM-backed keys / external keys | KMS / CloudHSM | Key Vault / Managed HSM |
| **Secret Manager** | Store API keys and passwords | Secrets Manager | Key Vault (secrets) |
| **Certificate Manager** | TLS certificates for load balancers | ACM | App Service Certificates / Key Vault |
| **Security Command Center** | Posture, findings, threat detection | Security Hub + GuardDuty | Defender for Cloud |
| **VPC Service Controls** | Data-exfiltration perimeters around Google APIs | (no direct equivalent; VPC endpoint policies + SCPs) | — |
| **Sensitive Data Protection** (formerly DLP API) | Discover and redact PII | Macie | Purview DLP |
| **Google Security Operations** | SIEM / SOAR | Security Lake + partner SIEM | Sentinel |
| **Organization Policy** | Guardrails on the org/folder/project | SCPs (Organizations) | Azure Policy |

## Operations and observability
| Google Cloud | Used for | AWS |
|--------------|----------|-----|
| **Cloud Monitoring** | Metrics, dashboards, alerting, uptime checks | CloudWatch |
| **Cloud Logging** | Central logs, log router/sinks | CloudWatch Logs |
| **Cloud Audit Logs** | Who did what, where, when | CloudTrail |
| **Cloud Trace / Cloud Profiler** | Distributed tracing / CPU and memory profiling | X-Ray / CodeGuru Profiler |
| **Error Reporting** | Aggregate app exceptions | CloudWatch (Application Signals) |

## DevOps and management
| Google Cloud | Used for | AWS |
|--------------|----------|-----|
| **Cloud Build** | CI builds | CodeBuild |
| **Artifact Registry** | Container images and packages | ECR / CodeArtifact |
| **Cloud Deploy** | Continuous delivery to GKE / Cloud Run | CodePipeline / CodeDeploy |
| **Infrastructure Manager** | Managed Terraform (successor to Deployment Manager) | CloudFormation |
| **Config Connector** | Manage GCP resources as Kubernetes objects | ACK (AWS Controllers for Kubernetes) |
| **Resource Manager** | Organization → folders → projects hierarchy | Organizations (OUs → accounts) |
| **Cloud Shell** | Browser shell with gcloud preinstalled | CloudShell |
| **Cloud Billing** | Budgets, billing export to BigQuery | Billing / Cost Explorer |

## Integration and APIs
| Google Cloud | Used for | AWS |
|--------------|----------|-----|
| **Apigee** | Full API management (quotas, analytics, security) | API Gateway (+ partner) |
| **API Gateway** | Lightweight managed gateway for serverless back ends | API Gateway |
| **Eventarc** | Event routing to Cloud Run and others | EventBridge |
| **Workflows** | Orchestrate service calls | Step Functions |
| **Cloud Tasks** | Async task queues with rate control | SQS |
| **Cloud Scheduler** | Managed cron | EventBridge Scheduler |

## AI and ML
| Google Cloud | Used for | AWS |
|--------------|----------|-----|
| **Vertex AI** | Train, tune, deploy models; Model Garden; Gemini API | SageMaker + Bedrock |
| **Gemini for Google Cloud** | AI assistant in the console and IDE | Amazon Q |
| **Document AI / Vision / Speech-to-Text / Translation** | Prebuilt ML APIs | Textract / Rekognition / Transcribe / Translate |

## Hybrid, multicloud and migration
| Google Cloud | Used for | AWS |
|--------------|----------|-----|
| **GKE Enterprise** (formerly Anthos) | Manage Kubernetes fleets across on-prem and clouds | EKS Anywhere |
| **Google Distributed Cloud** | GCP services on your hardware / edge | Outposts |
| **Migrate to Virtual Machines** | Lift-and-shift VMs | Application Migration Service |
| **Database Migration Service** | Migrate DBs to Cloud SQL / AlloyDB | DMS |
| **Storage Transfer Service / Transfer Appliance** | Bulk data transfer online / offline device | DataSync / Snowball |

---

## Big conceptual differences (GCP vs AWS)
| Topic | Google Cloud | AWS |
|-------|-------------|-----|
| Tenancy boundary | **Project** (inside folders and an organization) | **Account** (inside OUs) |
| VPC scope | **Global**; subnets are regional | Regional; subnets are per-AZ |
| Subnet scope | Regional (spans zones) | Single AZ |
| Firewall | VPC firewall rules / policies applied to **network tags or service accounts** | Security groups (per ENI) + NACLs (per subnet) |
| Global LB | One **anycast IP** worldwide (Premium tier) | Regional ALB/NLB; Global Accelerator for anycast |
| Backbone | Premium tier keeps traffic on Google's network to the closest edge | AWS global network (via Global Accelerator / CloudFront) |

## Linked Pages
- [[gcp-network-architecture]] · [[gcp-private-access]]
- [[certs/gcp-networking]] · [[GCP27 - Types of Complementary Partner Products]]
