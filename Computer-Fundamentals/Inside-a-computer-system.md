# Inside a Computer System

**Platform** : TryHackMe  
**Module** : Computer Fundamentals  

---

## Objective

Understand the core hardware components of a computer system, how they interconnect, and the full boot sequence from power button to operating system.

---

## Task 1 — Core Hardware Components

### Motherboard

The skeleton and nervous system of the computer. It holds all components in place and connects them. Every other component plugs into or communicates through the motherboard.

Key connectors on a typical desktop motherboard:
* CPU socket
* RAM slots
* PCIe expansion slots
* SATA connectors
* USB, audio and network ports

### CPU (Central Processing Unit)

The processor — executes instructions continuously. Modern CPUs have multiple cores that handle instructions in parallel. Connects to the motherboard via the CPU socket.

### RAM (Random Access Memory)

Short-term working memory. Holds data the CPU needs quick access to during active tasks. Key characteristics:

* **Volatile** : contents are lost when power is cut
* **Fast** : much faster access than storage
* **Technologies** : DDR5, DDR6

### Storage — HDD / SSD

Long-term memory. Data persists without power.

| Type | Technology | Speed | Cost |
|---|---|---|---|
| HDD | Spinning magnetic platters (moving parts) | Slower | Low cost per GB |
| SSD | Memory chips (no moving parts) | Much faster | Higher cost per GB |

Connection interfaces: SATA cables or PCIe slots.

### Network Adapter

Allows the computer to communicate with other systems. Can be:
* **Wired** (Ethernet)
* **Wireless** (Wi-Fi)

Often embedded in the motherboard. Can also be added as a PCIe expansion card.

### PSU (Power Supply Unit)

Converts AC power from the wall outlet into DC power for all components. Critical sizing consideration: if components require more power than the PSU can supply, the system will fail.

Key connectors: main motherboard connector, CPU connector, Molex connectors for drives.

### GPU (Graphics Card)

Processes visual data from the OS and applications and outputs it to a monitor. Connects via PCIe slots on the motherboard. Dedicated GPUs have their own VRAM and processing units.

### Input / Output (I/O)

| Category | Examples | Common connectors |
|---|---|---|
| Input | Keyboard, mouse, microphone, scanner | USB |
| Output | Monitor, printer, speakers | HDMI, DisplayPort, USB |

---

## Task 2 — The Boot Process

When the power button is pressed, the system follows a strict sequence before handing control to the operating system.

### Step-by-step

**Step 1 — Power Button**  
A signal is sent to the PSU to allow power to flow to all components.

**Step 2 — Firmware starts (UEFI)**  
The UEFI (Unified Extensible Firmware Interface) initialises all hardware components. Note: BIOS is the older equivalent largely replaced by UEFI. Both serve the same purpose.

**Step 3 — POST (Power-On Self Test)**  
The UEFI runs a diagnostic routine to verify that every required component is present, correctly configured, and functioning. If a component fails, the system signals an error (beep codes or on-screen messages).

**Step 4 — Select Boot Device**  
The UEFI holds an ordered list of boot devices (SSD, HDD, USB, network). It searches each in priority order for a valid bootloader.

**Step 5 — Bootloader initiates**  
The bootloader is located on the selected device. It loads the Operating System from storage into RAM. Once complete, the UEFI hands control of all hardware components to the OS.

### Boot sequence summary

```
Power Button
    ↓
PSU powers all components
    ↓
UEFI firmware initialises hardware
    ↓
POST — verify all components
    ↓
UEFI searches boot device list
    ↓
Bootloader loads OS into RAM
    ↓
OS takes control → working interface
```

---

## Component Summary Table

| Component | Analogy | Role | Connection |
|---|---|---|---|
| Motherboard | Skeleton / nervous system | Holds and connects everything | — |
| CPU | Brain | Executes instructions | CPU socket |
| RAM | Short-term memory | Fast temporary storage for active data | RAM slots |
| HDD / SSD | Long-term memory | Persistent data storage | SATA / PCIe |
| Network Adapter | Vocal cords | Communication with other systems | Embedded / PCIe |
| PSU | Heart | Powers all components | Main board connector |
| GPU | Visual cortex | Processes and outputs visual data | PCIe |
| I/O devices | Senses | Input and output with the user | USB / HDMI / DisplayPort |

---

## Security Relevance

| Component | Security consideration |
|---|---|
| UEFI / BIOS | Firmware attacks (bootkits) bypass OS-level security entirely — UEFI Secure Boot mitigates this |
| RAM | Volatile but exploitable live — cold boot attacks can extract encryption keys from powered-off RAM |
| Storage | Encryption at rest (BitLocker, LUKS) protects data if physical access is gained |
| Network Adapter | Entry point for network-based attacks — firmware vulnerabilities in NICs are a known attack vector |
| Boot process | Boot order misconfiguration can allow booting from external media to bypass authentication |
