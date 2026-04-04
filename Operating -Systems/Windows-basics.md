# Windows Basics

**Platform** : TryHackMe  
**Module** : Operating Systems Basics  
---

## Objective

Navigate the Windows graphical interface, manage files, configure system settings, and use built-in security tools on a Windows Server 2019 workstation.

---

## Task 1 — Introduction

Scenario: first day at TryHatMe. Tasks include navigating the Windows desktop, exploring system settings, installing applications, and running a security scan.

OS in use: **Windows Server 2019**

---

## Task 2 — Exploring the Windows Workspace

### Windows history

* **MS-DOS**: text-only command interface — black screen, typed commands
* **Windows 1.0 (1985)**: first GUI built on top of DOS — windows, menus, mouse
* **Modern Windows**: fully integrated OS evolved from that original shell

### Authentication and account types

Windows enforces identity verification before granting desktop access.

| Account type | Permissions |
|---|---|
| Guest | Minimal — temporary access, no system changes |
| Standard | Everyday tasks, personal settings, no system-wide changes |
| Administrator | Full control — software installation, configuration, user management |

Lab environment: automatically logged in as Administrator.

### The Windows Desktop

**The airport analogy:**

| Airport element | Windows equivalent |
|---|---|
| Security checkpoint | Login screen |
| Airport terminal | Desktop |
| Departure board / information desk | Start Menu |
| Directory and floor map | File Explorer |
| Airport services and amenities | Built-in tools and apps |

**Core desktop components:**

| Component | Function |
|---|---|
| Desktop | Main workspace — files, folders, shortcuts |
| Taskbar | Control strip — apps, tools, settings, notifications |
| Start Menu | Primary access point — apps, files, settings, power options |
| Search | Find apps, files, folders, settings by keyword |
| Task View | See all open windows, switch between them |
| Notification area | Network, audio, date/time, notifications |

### File system structure

Windows uses a hierarchical folder structure. Common locations:

```
C:\
├── Users\
│   └── Administrator\
│       ├── Desktop\
│       │   └── TryHatMe Onboarding\
│       ├── Documents\
│       └── Downloads\
├── Program Files\
└── Windows\
```

File Explorer shows the full path at the top of the window.  
Example: `C:\Users\Administrator\Desktop\TryHatMe Onboarding`

### Task answers

**Q: Device name?**  
[from VM — About your PC → Device specifications]

**Q: How much RAM is installed?**  
[from VM — Device specifications]

**Q: Which version of Windows Server 2019 Datacenter is installed?**  
[from VM — Windows specifications]

**Q: Flag in Welcome.txt?**  
[from VM — TryHatMe Onboarding folder]

---

## Task 3 — Configuring and Securing Windows

### Application management

**The airport analogy:**
* Updating OS/apps = changing flights or reserving a seat
* Installing a new app = scheduling a new flight
* Removing an app = canceling a booking

**Windows Update**: built-in tool that keeps the OS, native apps, and security features current. Includes security patches, performance updates, and bug fixes.

**Installing applications:**

| Method | Description |
|---|---|
| Microsoft Store | Curated, safe — not available by default on Windows Server |
| From the internet | Download `.exe` or `.msi` installer from trusted vendor website |

**Uninstalling applications:**

* Microsoft Store (for Store apps)
* Settings → Add or remove programs
* Control Panel → Uninstall a program
* Application's built-in uninstaller

### Settings and configuration

| Tool | Purpose |
|---|---|
| Windows Settings | Modern centralized interface — system, device, personalization, security |
| Control Panel | Legacy interface — older administrative tools still required for specific tasks |

### Task Manager

Real-time system monitoring tool. Five tabs:

| Tab | Content |
|---|---|
| Processes | Running apps and background processes with resource usage |
| Performance | CPU, memory, network graphs and statistics |
| Users | Logged-in users and their resource usage |
| Details | Technical view — process IDs (PIDs) |
| Services | Windows services and their status (running/stopped) |

### Native Windows Security

**Windows Security** — central dashboard for built-in protections:

| Section | Function |
|---|---|
| Virus & threat protection | Detects and removes malware, real-time protection, custom scans |
| Firewall & network protection | Controls incoming and outgoing network traffic |
| App & browser control | Protects against unsafe apps, files, and websites |
| Device security | Hardware-based system protections |

**Custom scan workflow:**  
Windows Security → Virus & Threat Protection → Scan options → Custom scan → Select TryHatMe Onboarding folder

Test file detected: `Virus:DOS/EICAR_Test_File` — a safe standardised test file used to verify antivirus functionality.

**Windows Defender Firewall** — built-in firewall managing network traffic:

| Profile | Use case |
|---|---|
| Domain | Connected to an organisation's domain network |
| Private | Trusted networks (home, lab) |
| Public | Untrusted networks (public Wi-Fi) |

Advanced settings allow viewing and creating inbound, outbound, and connection rules.

### Task answers

**Q: Flag after installing TryHatMeWelcome?**  
[from VM]

**Q: Country/region in Time & Language settings?**  
[from VM]

**Q: Account shown in Task Manager → Users tab?**  
[from VM]

**Q: File name in Affected items after EICAR scan?**  
[from VM]

---

## Task 4 — Key Concepts Summary

| Term | Definition |
|---|---|
| Desktop | Main workspace — files, folders, shortcuts |
| Taskbar | Control strip — apps, tools, settings, notifications |
| Start Menu | Primary access — apps, settings, power options |
| Search | Locates apps, settings, files by keyword |
| File Explorer | Built-in tool to browse, manage, and organise files |
| Windows Update | Keeps OS, native apps, and security features up to date |
| Microsoft Store | Native app store for installing trusted applications |
| Windows Settings | Centralised system configuration |
| Control Panel | Legacy management interface |
| Task Manager | Real-time system monitoring |
| Windows Security | Central dashboard for built-in security tools |
| Windows Defender Firewall | Protects against unauthorised network traffic |

---

## Security Relevance

| Component | Security consideration |
|---|---|
| Administrator account | Default admin login = maximum attack surface. Principle of least privilege recommends using standard accounts for daily use |
| Windows Update | Unpatched systems are the primary attack vector in enterprise environments — keep OS and apps current |
| Windows Defender / AV | EICAR test validates AV is functional. Real-time protection must remain enabled |
| Firewall profiles | Public profile is most restrictive — always verify profile when connecting to new networks |
| Task Manager PIDs | Suspicious processes (unusual names, high CPU/memory, unknown paths) are a key IOC during incident response |
| File paths | Malware often drops files in `C:\Users\<user>\AppData\` or `C:\Temp\` — File Explorer visibility into these paths is essential |
