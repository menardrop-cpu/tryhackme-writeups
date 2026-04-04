# Cloud Computing Fundamentals

**Platform** : TryHackMe  
**Module** : Computer Fundamentals  

---

## Objective

Understand the core concepts of cloud computing, the different deployment and service models, and how to deploy and manage cloud resources in a real-world scenario using an AWS-like interface.

---

## Task 1 — Introduction

Cloud computing solves the fundamental limitations of running applications on physical hardware. Instead of being tied to a single machine in a single location, the cloud enables computing resources to be accessed over the internet, making applications more accessible, reliable, and scalable.

---

## Task 2 — Cloud Computing Overview

### Evolution of servers

Cloud computing did not appear suddenly — it is the result of decades of infrastructure evolution:

```
Physical servers
    ↓
Virtualisation (multiple VMs per server)
    ↓
Managed hosting
    ↓
Cloud computing (on-demand, pay-per-use, globally distributed)
```

### Cloud benefits and characteristics

| Benefit | Description |
|---|---|
| Scalability | Scale up or down instantly based on demand |
| On-demand self-service | Create or remove resources instantly without hardware procurement |
| Pay only for what you use | Charged based on actual usage, no upfront costs |
| Security | Cloud providers maintain strong infrastructure security |
| High availability | Applications stay online even if part of the system fails |
| Global access | Users anywhere in the world can reach your application |

### Cloud deployment types

| Type | Description | Typical users |
|---|---|---|
| Public Cloud | Shared infrastructure over the internet | Startups, websites, global apps |
| Private Cloud | Dedicated infrastructure for one organisation | Banks, healthcare, government |
| Hybrid Cloud | Mix of public and private, sharing data between both | E-commerce platforms balancing sensitive data with scalability |

### Cloud service models

| Model | Who manages what | Examples |
|---|---|---|
| IaaS (Infrastructure as a Service) | Provider: hardware. You: OS, apps | AWS EC2, Azure VMs |
| PaaS (Platform as a Service) | Provider: hardware + OS. You: apps | Heroku, Google App Engine |
| SaaS (Software as a Service) | Provider: everything. You: use it | Gmail, Zoom, Salesforce |

**The apartment analogy:**

* IaaS = you rent an empty apartment, furnish and decorate it yourself
* PaaS = you rent a furnished apartment, bring your own belongings
* SaaS = you stay in a hotel, everything is provided

### Major cloud vendors

| Vendor | Strength |
|---|---|
| AWS (Amazon Web Services) | Industry leader, widest offering, global reach |
| Microsoft Azure | Enterprise and hybrid cloud environments |
| Google Cloud Platform | Data analytics, AI, machine learning |
| Alibaba Cloud | Dominant in Asia, competitive globally |
| IBM Cloud | Hybrid cloud and AI for businesses |
| Oracle Cloud | Enterprise applications and databases |

### Real-world examples

* **Netflix**: entire platform on AWS — scales globally, streams to millions simultaneously
* **Spotify**: handles millions of songs and users, scales on new releases
* **Instagram**: stores massive photo/video volumes, delivers globally at speed
* **E-commerce**: absorbs Black Friday traffic spikes without permanent infrastructure

---

## Task 3 — Deploying a Cloud Instance

### Key terminology

**EC2 (Elastic Compute Cloud)**: AWS's virtual computer service. Each EC2 instance is a virtual server with its own CPU and RAM that can run applications.

**Instance types**: define how powerful an EC2 instance is.

| Rule | Effect |
|---|---|
| Larger instance type | More CPU and RAM, higher cost |
| Smaller instance type | Less CPU and RAM, lower cost |

### Environment deployed

Three EC2 instances created to host a cybersecurity training application (IaaS model — full OS access required to install tools and simulate attacks):

| Instance Name | Instance Type | Purpose |
|---|---|---|
| application-interface | t3.micro | Application frontend |
| study-machine-1 | m5.large | User practice environment |
| study-machine-2 | m5.large | User practice environment |

**Region**: selected based on target geographic location (e.g. Europe, North America).

### Billing analysis

Cost-optimisation exercise: since the application is still in development and the study machines have no active users, stopping them reduces the monthly cost.

**Actions:**
* Stopped `study-machine-1` and `study-machine-2`
* Reviewed billing impact in the Billing section

| Scenario | Monthly cost |
|---|---|
| All 3 instances running | [from interface] |
| study-machine-1 and 2 stopped | [from interface] |
| Only new instances running | [from interface] |
| Adding a third t3a.small study machine | [from interface] |

> Replace brackets above with values from the interface.

**Key lesson**: cloud costs can be adjusted in real time by starting and stopping instances — a fundamental advantage over physical infrastructure where hardware costs are fixed.

---

## Task 4 — Key Concepts Summary

| Term | Definition |
|---|---|
| Public Cloud | Cloud services shared over the internet by many users |
| Private Cloud | Dedicated cloud for a single organisation |
| Hybrid Cloud | Mix of public and private cloud that can share data |
| IaaS | Rent basic compute resources — you manage the OS and apps |
| PaaS | Ready-to-use environment to build apps — provider manages servers |
| SaaS | Software used online without installation (Gmail, Zoom) |
| EC2 | Amazon's virtual computers — create, resize, and stop on demand |

**Key benefits of cloud computing:**
Scalability, on-demand self-service, pay-per-use, security, high availability, global access.

---

## Security Relevance

| Concept | Security consideration |
|---|---|
| Public cloud | Shared infrastructure — data isolation between tenants is critical |
| Private cloud | Greater control but full security responsibility falls on the organisation |
| IaaS | OS-level access = full attack surface — hardening and patching are the tenant's responsibility |
| PaaS | Reduced attack surface but less visibility — security depends on provider practices |
| SaaS | No infrastructure control — data governance and access management are critical |
| Region selection | Data sovereignty — storing data in certain regions may trigger legal obligations (RGPD, NIS2) |
| Instance stopping | Stopped instances reduce attack surface but data at rest remains — encryption essential |
