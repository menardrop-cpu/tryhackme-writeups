# Virtualisation Basics

**Platform** : TryHackMe  
**Module** : Computer Fundamentals  

---

## Objective

Understand why virtualisation powers modern IT, how it improves hardware efficiency, and how to manage virtual machines in a real-world environment. Covers hypervisors, VMs, containers, and hands-on VM management.

---

## Task 1 — Introduction

Before virtualisation, the standard in IT was: **one server = one application**.

This approach caused serious inefficiencies:

* **High cost**: hardware, electricity, cooling, maintenance and data centre space multiplied with every new service
* **Low utilisation**: most servers ran at 5–20% of their capacity
* **Slow deployment**: provisioning new physical servers could take days or weeks
* **Hard to scale**: sudden resource needs required purchasing new hardware

Virtualisation solved this by allowing multiple applications to safely share the same physical hardware through a software layer called a **hypervisor**.

### The Building Analogy

| Physical world | Virtualisation equivalent |
|---|---|
| The building | Physical server |
| The apartments | Virtual Machines |
| The tenants | Applications / Operating Systems |
| The building manager | Hypervisor |

---

## Task 2 — Virtualisation Overview

### Hypervisor

The hypervisor is the core technology behind virtualisation. It:

* Divides a physical computer into multiple virtual ones
* Allocates CPU, memory and storage to each VM
* Keeps VMs isolated from each other
* Manages the VM lifecycle: start, stop, pause, clone, delete

**Two types of hypervisors:**

| Feature | Type 1 | Type 2 |
|---|---|---|
| Runs on | Physical hardware directly | Existing operating system |
| Performance | High | Moderate |
| Use case | Production servers, data centres | Labs, testing, learning |
| Examples | VMware ESXi, Microsoft Hyper-V | VirtualBox, VMware Workstation |

**Use case mapping:**

| Use Case | Type 1 | Type 2 |
|---|---|---|
| Test malicious files | | ✅ |
| Production server | ✅ | |
| Database server | ✅ | |
| Software testing | | ✅ |
| Kali Linux lab | | ✅ |
| Data centre | ✅ | |

### Virtual Machines

A VM is a virtual computer created by the hypervisor. Even though virtual, it behaves exactly like a physical machine:

* Has its own virtual CPU, RAM, storage and network adapter
* Can run any operating system (Windows, Linux, macOS)
* Completely isolated from other VMs — if one breaks, the others keep running

Common use cases:
* Running Kali Linux on a Windows host without buying additional hardware
* Testing potentially malicious files in an isolated environment

### Containers

A container is a lightweight, isolated environment that runs a single application and all its dependencies.

Key differences from VMs:

* Containers share the host OS kernel — no full guest OS required
* Start almost instantly and consume far fewer resources
* Must match the host system type (no Windows containers on Linux)
* Ideal for scalable, fast-deploying applications

**Docker** is the most common platform for deploying containers.

### Architecture overview

```
Physical Server
└── Hypervisor
    ├── Virtual Machine 1
    │   ├── Guest OS
    │   └── Application
    └── Virtual Machine 2
        ├── Guest OS
        ├── Container 1 (App A)
        └── Container 2 (App B)
```

### Task answers

**Q: A user wants to deploy a study lab for a cybersecurity certification. Which hypervisor type?**  
`Type 2`

**Q: A company wants to host multiple small applications in the same VM. What should they use?**  
`Containers`

---

## Task 3 — Hands-On: VM Management

Scenario: Hired as virtualisation administrator for **AutoGalo**, managing their virtual environment via **Virtualization Manager**.

### Analysing VM states

The `Mail-SERVER` VM was found in an `Error` state, causing the company-wide email outage.

Resolution: restarted the VM via the interface → service restored.

This demonstrates VM lifecycle management: a core responsibility of virtualisation administrators.

### Creating a Virtual Machine

New VM provisioned for the marketing team's website:

| Parameter | Value |
|---|---|
| Name | Marketing-VM |
| CPU Cores | 4 |
| Memory | 8 GB |
| Disk Size | 100 GB |

### Analysing host hardware usage

| Host | Status | Notes |
|---|---|---|
| HV-PROD-01 | Healthy | Capacity available, can host more VMs |
| HV-PROD-02 | ⚠️ Near 100% | At capacity — must be reported to management |
| HV-BACKUP-01 | Disconnected | No VMs hosted, unavailable |

Key takeaway: monitoring physical host utilisation is critical to prevent performance degradation and outages.

---

## Task 4 — Key Concepts Summary

| Term | Definition |
|---|---|
| Virtualisation | Enables one physical computer to act as multiple independent systems |
| Hypervisor | Software that creates and manages virtual machines |
| Virtual Machine (VM) | A complete virtual computer with its own OS and resources |
| Container | Lightweight isolated environment for a single app, sharing the host kernel |
| Container Image | A pre-packaged template used to create containers |
| Network Ports | Numbered entry points that applications use to communicate over a network |

**Key benefits of virtualisation:**

* Cost savings
* Better resource utilisation
* Safe environment for cybersecurity testing
* Faster deployment
* Flexibility and portability
* Scalability
* Centralised management

---

## Security Relevance

| Concept | Security consideration |
|---|---|
| VM isolation | If one VM is compromised, others are unaffected — critical for malware analysis labs |
| Type 2 hypervisors | Ideal for testing malicious files safely; ensure guest/host isolation and disable shared folders |
| Containers | Shared kernel increases attack surface — a kernel exploit can affect all containers on a host |
| Snapshot capability | VMs can be rolled back to a clean state after testing — essential for malware analysis workflows |
| Host saturation (HV-PROD-02) | Overloaded hypervisors can cause VM crashes — a DoS vector without any external attacker |
