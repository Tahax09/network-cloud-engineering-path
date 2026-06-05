---
id: GCP05
title: "Lab: Traffic Steering Using Geolocation"
course: Google Cloud Networking Specialization
cert_track: gcp-networking
module: Routing & Addressing
date: 2025-05-28
status: Complete
quiz_attempted: No
weak_areas: []
tags: [gcp, dns, geolocation, lab, cloud-dns, routing-policy, dig]
---

# GCP05 — Lab: Traffic Steering Using Geolocation

## Objective
Configure a Cloud DNS private zone with a geolocation routing policy. Same hostname returns different server IPs depending on query source region. Tests nearest-match fallback for an unmapped region.

## Architecture

| Client Region | Expected DNS Result | Why |
|--------------|----------------------|-----|
| us-central1 | US server IP | Exact match |
| europe-west1 | EU server IP | Exact match |
| asia-east1 | Asia server IP | Exact match |
| asia-south1 | Asia server IP | No mapping → nearest match (asia-east1) |

## Key Commands

```bash
# Set project
gcloud config set project YOUR_PROJECT_ID
export NETWORK=default
export ZONE_NAME=example-private-zone
export DNS_DOMAIN=example.com.   # trailing dot required

# Create server VMs (us-central1, europe-west1, asia-east1 — NOT asia-south1)
gcloud compute instances create server-us \
  --zone=us-central1-a --machine-type=e2-micro \
  --tags=http-server

# Capture internal IPs
export IP_US=$(gcloud compute instances describe server-us \
  --zone=us-central1-a --format='get(networkInterfaces[0].networkIP)')
export IP_EU=$(gcloud compute instances describe server-eu \
  --zone=europe-west1-b --format='get(networkInterfaces[0].networkIP)')
export IP_ASIA=$(gcloud compute instances describe server-asia \
  --zone=asia-east1-b --format='get(networkInterfaces[0].networkIP)')

# Create private DNS zone
gcloud dns managed-zones create ${ZONE_NAME} \
  --dns-name=${DNS_DOMAIN} \
  --networks=${NETWORK} \
  --visibility=private

# Create geolocation routing policy record
gcloud dns record-sets create server.${DNS_DOMAIN} \
  --zone=${ZONE_NAME} --type=A --ttl=5 \
  --routing-policy-type=GEO \
  --routing-policy-data="us-central1=${IP_US};europe-west1=${IP_EU};asia-east1=${IP_ASIA}"

# Test from each client
gcloud compute ssh client-us --zone=us-central1-a \
  --command="dig server.example.com +short"
gcloud compute ssh client-eu --zone=europe-west1-b \
  --command="dig server.example.com +short"
gcloud compute ssh client-asia --zone=asia-east1-b \
  --command="dig server.example.com +short"

# Test nearest-match from asia-south1 (no explicit mapping)
gcloud compute ssh client-asia-south --zone=asia-south1-a \
  --command="dig server.example.com +short"
# Expected: Asia server IP (nearest match — asia-east1)
```

## What to Observe
- **Exact match regions:** Each client resolves to its region's IP
- **Nearest-match (asia-south1):** No mapping → Cloud DNS returns geographically nearest configured target (asia-east1 for Mumbai)
- **Private zone isolation:** Querying from outside the VPC returns NXDOMAIN — confirms private zone
- **Low TTL (5s):** Prevents caching during lab testing. Use 300+ seconds in production

## Key Lesson
You don't need to enumerate every source region. The nearest-match guarantee means all clients receive a response even without an explicit mapping.

## Related
- [[GCP04]] — Cloud DNS routing policies (theory)
- [[GCP06]] — module quick reference
