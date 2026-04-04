# Linux CLI Basics

**Platform** : TryHackMe  
**Module** : Operating Systems Basics  

---

## Objective

Get comfortable navigating the Linux CLI. Navigate the filesystem, search for files, inspect system information, and read configuration files — the foundational skills used daily in cybersecurity.

---

## Task 1 — Introduction

Context: first day as IT Support Engineer on a Cyber Operations Support Team. Supervisor left in a hurry, leaving a note to get started alone with the terminal.

**Why Linux matters in cybersecurity:**
* Powers the majority of servers, cloud infrastructure, and security tools
* Most attack tools and defense tools run natively on Linux
* CLI gives more control, precision, and speed than a GUI

**The terminal**: text-based interface for controlling a Linux system. Type commands that tell the computer exactly what to do.

---

## Task 2 — Navigation Mission: "Find the Missing Notes"

### Core navigation commands

**`pwd` — Print Working Directory**

Shows the current folder location.

```bash
ubuntu@tryhackme:~$ pwd
/home/ubuntu
```

**`ls` — List directory contents**

```bash
ubuntu@tryhackme:~$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos  logs  projects
```

With details (permissions, size, dates):

```bash
ubuntu@tryhackme:~$ ls -l
drwxr-xr-x 2 ubuntu ubuntu 4096 Feb 27  2022 Desktop
drwxr-xr-x 6 ubuntu ubuntu 4096 Dec 11 12:45 Documents
...
```

With hidden files (files starting with `.`):

```bash
ubuntu@tryhackme:~$ ls -al
drwxr-xr-x 24 ubuntu ubuntu 4096 Feb 10 10:48 .
drwxr-xr-x  3 root   root   4096 Feb 10 10:36 ..
-rw-------  1 ubuntu ubuntu  439 Feb 10 06:47 .Xauthority
...
```

Hidden files in Linux start with a `.` and are not displayed by default.

**`cd` — Change Directory**

```bash
ubuntu@tryhackme:~$ cd Documents/
ubuntu@tryhackme:~/Documents$ pwd
/home/ubuntu/Documents

ubuntu@tryhackme:~/Documents$ cd ..     # go back one level
ubuntu@tryhackme:~$
```

**`find` — Locate files in the filesystem**

```bash
ubuntu@tryhackme:~$ find ~ -name mission_brief.txt
/[path]/mission_brief.txt
```

Syntax: `find <starting_point> -name <filename>`  
`~` = home directory

**`cat` — Read file contents**

```bash
ubuntu@tryhackme:~$ cat mission_brief.txt
```

### Task answers

**Q: Full path of mission_brief.txt?**  
[from terminal — output of `find ~ -name mission_brief.txt`]

**Q: Flag inside mission_brief.txt?**  
[from terminal — output of `cat mission_brief.txt`]

---

## Task 3 — Investigating the System

### System information commands

**`whoami` — Current logged-in user**

```bash
ubuntu@tryhackme:~$ whoami
ubuntu
```

**`uname -a` — Full system information**

```bash
ubuntu@tryhackme:~$ uname -a
Linux tryhackme <kernel-version> x86_64 x86_64 x86_64 GNU/Linux
```

Output breakdown:

| Field | Meaning |
|---|---|
| `Linux` | Kernel type |
| `tryhackme` | Hostname |
| `<kernel-version>` | Kernel version installed |
| `x86_64` | Hardware architecture (64-bit) |
| `GNU/Linux` | OS type (Linux kernel + GNU tools) |

**`df -h` — Disk usage (human readable)**

```bash
ubuntu@tryhackme:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        --G   12G   --G  17% /
tmpfs           1.9G     0  1.9G   0% /dev/shm
```

* `-h` = human readable (shows GB, MB instead of bytes)
* `/dev/root` = main system disk
* `tmpfs` = temporary filesystems stored in RAM, not on physical disk

**`cat /etc/os-release` — Linux distribution info**

```bash
ubuntu@tryhackme:/etc$ cat os-release
PRETTY_NAME="Ubuntu 24.04.1 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.1 LTS (Noble Numbat)"
```

The `/etc` directory contains configuration and system information files.

### Mini challenge — day1_report.txt

```bash
find ~ -name day1_report.txt       # locate the file
cd /path/to/found/directory        # navigate to it
cat day1_report.txt                # read the contents
```

### Task answers

**Q: Username from whoami?**  
[from terminal]

**Q: Kernel version from uname -a?**  
[from terminal]

**Q: Free disk space from df -h?**  
[from terminal — Avail column on /dev/root]

**Q: Message inside day1_report.txt?**  
[from terminal]

---

## Task 4 — Key Concepts Summary

### Commands reference table

| Command | Function | Example |
|---|---|---|
| `pwd` | Show current directory | `pwd` → `/home/ubuntu` |
| `ls` | List directory contents | `ls -al` for all files with details |
| `cd <dir>` | Change directory | `cd Documents` |
| `cd ..` | Go back one level | `cd ..` |
| `find ~ -name <file>` | Search for a file | `find ~ -name mission_brief.txt` |
| `cat <file>` | Read file contents | `cat mission_brief.txt` |
| `whoami` | Show current username | `whoami` → `ubuntu` |
| `uname -a` | Show full system info | Kernel, hostname, architecture |
| `df -h` | Show disk usage | Human-readable sizes |

### Filesystem structure overview

```
/                   Root of the filesystem
├── home/           User home directories
│   └── ubuntu/     Current user's home (~)
├── etc/            Configuration files
├── var/            Variable data (logs, databases)
├── tmp/            Temporary files
├── dev/            Device files
└── proc/           Kernel and process information
```

---

## Security Relevance

| Command | Security use case |
|---|---|
| `find` | Locate suspicious files, recently modified files (`find / -mtime -1`) |
| `ls -al` | Spot hidden files (dotfiles), check file permissions |
| `cat` | Read log files, configuration files, dropped payloads |
| `whoami` | Verify current user context — critical after exploitation |
| `uname -a` | Identify kernel version to check for known CVEs |
| `df -h` | Check available space before running tools or analysis |
| `/etc/os-release` | Identify distribution and version for vulnerability research |
