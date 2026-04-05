# Windows CLI Basics

**Platform** : TryHackMe  
**Module** : Operating Systems Basics  

---

## Objective

Use the Windows Command Prompt to navigate the filesystem, find files, read their contents, and gather system and network information — foundational skills for IT support and cybersecurity investigations.

---

## Task 1 — Introduction

Context: Day 2 of internship. Supervisor has left another task to complete using only the Windows CLI.

**What is the Command Prompt (CMD)?**  
A text-based interface for interacting with the Windows OS. Type commands instead of clicking menus. Faster, more precise, and easier to automate than the GUI.

**Why CMD matters in cybersecurity:**
* Not everything is visible in the GUI
* Files may be hidden or buried deep in the system
* Analysts need quick answers without navigating menus
* CLI tools are faster, more precise, and scriptable

---

## Task 2 — Navigating Files and Finding Your First File

### Core navigation commands

**`cd` — Show or change current directory**

```cmd
C:\Users\Administrator> cd
C:\Users\Administrator
```

On Windows, `cd` alone prints the current directory. `cd <folder>` moves into it. `cd ..` goes back one level.

**`dir` — List directory contents**

```cmd
C:\Users\Administrator> dir
```

Lists files and folders visible by default.

**`dir /a` — Show hidden files and folders**

```cmd
C:\Users\Administrator> dir /a
```

`/a` shows all items including those marked as hidden. Hidden doesn't mean secret — Windows just hides them by default.

**`dir /s <filename>` — Search recursively for a file**

```cmd
C:\Users\Administrator> dir /s task_brief.txt
```

`/s` searches all subfolders from the current directory. Returns the full path if the file exists.

**`type <filename>` — Read file contents**

```cmd
C:\Users\Administrator> type task_brief.txt
```

Equivalent of Linux `cat`. Prints the file content directly in the terminal.

### Step-by-step workflow

```
1. cd              → confirm current location
2. dir             → see visible contents
3. dir /a          → reveal hidden items
4. dir /s task_brief.txt  → locate the file
5. cd <path>       → navigate to the file
6. dir             → confirm file is present
7. type task_brief.txt    → read the contents
```

### Task answers

**Q: Full path of task_brief.txt?**  
[from VM — output of `dir /s task_brief.txt`]

**Q: Message and flag inside task_brief.txt?**  
[from VM — output of `type task_brief.txt`]

---

## Task 3 — Gathering System Information

### System information commands

**`whoami` — Current logged-in user**

```cmd
C:\Users\Administrator> whoami
desktop-abc\administrator
```

Shows `hostname\username`. Different users have different permissions — knowing which account you are using is critical before any action.

**`hostname` — Computer name**

```cmd
C:\Users\Administrator> hostname
DESKTOP-ABC123
```

Every Windows machine has a name. In organisations, this identifies the machine on the network.

**`systeminfo` — Full system details**

```cmd
C:\Users\Administrator> systeminfo
```

Key fields to focus on:

| Field | Information |
|---|---|
| OS Name | Windows version (e.g. Windows Server 2019) |
| OS Version | Build number |
| System Type | x64-based PC (architecture) |
| Total Physical Memory | RAM installed |

**`ipconfig` — Network configuration**

```cmd
C:\Users\Administrator> ipconfig
```

Key fields:

| Field | Information |
|---|---|
| IPv4 Address | Machine's IP on the local network |
| Default Gateway | Router IP — entry/exit point for network traffic |
| Subnet Mask | Defines the network range |

### Task answers

**Q: Computer name from hostname?**  
[from VM]

**Q: Windows version from systeminfo?**  
[from VM]

---

## Task 4 — Key Concepts Summary

### CMD vs Linux CLI comparison

| Action | Linux command | Windows CMD |
|---|---|---|
| Show current directory | `pwd` | `cd` |
| List contents | `ls` | `dir` |
| List with hidden files | `ls -al` | `dir /a` |
| Change directory | `cd <dir>` | `cd <dir>` |
| Go back one level | `cd ..` | `cd ..` |
| Search for a file | `find ~ -name file.txt` | `dir /s file.txt` |
| Read a file | `cat file.txt` | `type file.txt` |
| Current user | `whoami` | `whoami` |
| System info | `uname -a` | `systeminfo` |
| Network info | `ip a` / `ifconfig` | `ipconfig` |
| Hostname | `hostname` | `hostname` |

### Commands reference

| Command | Function |
|---|---|
| `cd` | Show or change current directory |
| `dir` | List directory contents |
| `dir /a` | List including hidden files |
| `dir /s <file>` | Search recursively for a file |
| `type <file>` | Read file contents |
| `whoami` | Current logged-in user |
| `hostname` | Computer name |
| `systeminfo` | Full OS and hardware details |
| `ipconfig` | Network configuration |

---

## Security Relevance

| Command | Security use case |
|---|---|
| `dir /a` | Reveal hidden malware files or persistence mechanisms |
| `dir /s` | Locate dropped payloads or suspicious files by name |
| `type` | Read suspicious scripts, config files, or dropped notes |
| `whoami` | Verify privilege level — `administrator` vs standard user |
| `systeminfo` | Identify OS version → check for unpatched CVEs |
| `ipconfig` | Map network placement, identify interfaces and gateway |
| `hostname` | Correlate machine name with logs during investigation |
