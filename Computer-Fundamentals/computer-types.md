# Computer Types

**Platform** : TryHackMe  
**Module** : Computer Fundamentals  

---

## Objective

Explore the different types of computers, from laptops and servers to the tiny embedded chips inside everyday objects.

---

## Task 1 — Introduction

Not all computers are designed to be sat in front of. Not all computers are meant to move. Computers come in many forms, each built for a specific purpose and context.

---

## Task 2 — The Computers You Sit In Front Of

### Traditional computing devices

| Computer Type | Screen & Keyboard | Main Purpose |
|---|---|---|
| Laptop | Yes | Portable everyday computing |
| Desktop | Yes | Sustained performance at a fixed location |
| Workstation | Yes | Precision and reliability for professional tasks |
| Server | No | Providing services to many users over a network |

### Key differences

**Laptop**  
Built for portability. Runs on battery and must stay cool inside a compact enclosure. Trade-off: sustained heavy workloads cause thermal throttling and performance drops.

**Desktop**  
Fixed location, wall-powered, better cooling. Designed for consistency over mobility. Handles long tasks more reliably than a laptop.

**Workstation**  
Looks like a desktop but built to different standards. Prioritises accuracy and reliability for professional tasks: simulations, 3D modelling, scientific computing. Uses specialised components to minimise errors during long or complex computations.

**Server**  
No screen, no keyboard. Runs continuously and answers requests from multiple users simultaneously. Users never interact with them directly — servers power the tools and services people use every day.

---

## Task 3 — Hidden Computers

### Pocket-sized and everyday devices

Beyond traditional computers, millions of computing devices hide inside everyday objects.

| Type | What it is | Examples |
|---|---|---|
| Smartphone | Pocket-sized computer optimised for battery life and connectivity | iPhone, Android phones |
| Tablet | Touch-first computer with a larger screen | iPad, drawing tablets |
| IoT device | Network-connected device with a single purpose | Smart thermostat, doorbell, fitness tracker |
| Embedded computer | Computer built directly into another device | Coffee maker controller, automatic door sensor, lamp dimmer chip |

### IoT vs Embedded — Key distinction

Both can be small and single-purpose. The difference is **connectivity**.

| | IoT Device | Embedded Computer |
|---|---|---|
| Network connection | Yes — reports data or receives commands | Not necessarily |
| Visibility | Often managed via app or dashboard | Often invisible, no user interface |
| Examples | Smart doorbell, connected thermostat | Door sensor motor controller, microwave timer chip |
| Lifespan without interaction | Regularly updated | Can run for years without anyone knowing it exists |

### Real-world example

Automatic doors at Nova Labs: a tiny embedded computer inside the door frame detects movement and signals the motor to open. Invisible, reliable, no network required — pure embedded computing.

---

## Summary Table

| Type | Portable | Network | Screen | Primary User |
|---|---|---|---|---|
| Laptop | ✅ | ✅ | ✅ | Individual user |
| Desktop | ❌ | ✅ | ✅ | Individual user |
| Workstation | ❌ | ✅ | ✅ | Professional / engineer |
| Server | ❌ | ✅ | ❌ | Multiple users simultaneously |
| Smartphone | ✅ | ✅ | ✅ | Individual user |
| Tablet | ✅ | ✅ | ✅ | Individual user |
| IoT device | Varies | ✅ | ❌ | Automated / remote |
| Embedded computer | ❌ | Not always | ❌ | None — autonomous |

---

## Security Relevance

| Device Type | Security consideration |
|---|---|
| Laptop | Theft risk — encryption at rest, remote wipe essential |
| Workstation | High-value target — privileged access, local data sensitivity |
| Server | Primary attack target — hardening, patching, access control critical |
| IoT device | Weak default credentials, infrequent updates, large attack surface |
| Embedded computer | Rarely updated, no security monitoring, physically accessible in public spaces |
