# OSI Model

**Platform** : TryHackMe
**Difficulty** : Easy
**Time** : 30 min
**Status** : Completed 100%
**Path** : Pre Security > Network Fundamentals

---

## Objective

Understand the OSI (Open Systems Interconnection) model : the 7-layer
framework that defines how data is handled and transmitted across a network.

---

## Tasks covered

* Task 1 : What is the OSI Model?
* Task 2 : Layer 1 - Physical
* Task 3 : Layer 2 - Data Link
* Task 4 : Layer 3 - Network
* Task 5 : Layer 4 - Transport
* Task 6 : Layer 5 - Session
* Task 7 : Layer 6 - Presentation
* Task 8 : Layer 7 - Application
* Task 9 : Practical - OSI Game

---

## What is the OSI Model?

The OSI model is a universal reference framework that breaks down network
communication into 7 independent layers, each with a specific role.

Two purposes : standardise communication between devices from different
manufacturers, and provide a structured way to diagnose exactly where a
problem occurs on a network.

**Mnemonic (bottom to top)**
Please Do Not Throw Sausage Pizza Away
Physical, Data Link, Network, Transport, Session, Presentation, Application

---

## The 7 Layers

### Layer 1 : Physical

Handles the raw physical transmission of data as electrical signals,
light pulses, or radio waves through a physical medium.

Does not care about the meaning of the data, only the signal.

**Mediums** : Ethernet cable (electrical), fibre optic (light), WiFi (radio waves)

**Devices** : Hubs, repeaters, cables, wireless access points

**Security risks**
* Physical wiretapping on cables
* WiFi signal jamming
* Physical destruction of infrastructure

---

### Layer 2 : Data Link

Handles communication between two directly connected devices on the same
local network. Responsible for physical addressing (MAC addresses) and
error detection in transmission.

**Two sub-layers**
* LLC (Logical Link Control) : flow control and error detection
* MAC (Media Access Control) : physical addressing and medium access

**Devices** : Switches operate at layer 2. They read MAC addresses to
direct frames to the correct port.

**Protocols** : Ethernet, WiFi (802.11), ARP

**Security risks**
* ARP Spoofing : poisoning ARP caches to intercept traffic
* MAC Flooding : overwhelming a switch's MAC table to force it to broadcast
* VLAN Hopping : accessing a VLAN the attacker should not have access to

---

### Layer 3 : Network

Handles logical addressing (IP addresses) and routing of packets between
different networks. Decides the best path for data to travel across the Internet.

**Devices** : Routers operate at layer 3. They read IP addresses and route packets.

**Protocols** : IP (IPv4, IPv6), ICMP (used by ping), IPSec

**Security risks**
* IP Spoofing : falsifying the source IP address
* Routing table manipulation
* ICMP Flood (Ping Flood) : saturating a target with ICMP requests
* Packet fragmentation attacks

---

### Layer 4 : Transport

Handles end-to-end transmission between two devices. Segments data,
manages flow control, and ensures reliable delivery or fast delivery
depending on the protocol used.

**The two main protocols**

**TCP (Transmission Control Protocol)**
Connection-oriented. Establishes a connection before sending data,
verifies receipt of every segment, and retransmits lost ones.
Reliable but slower.

The 3-way handshake :
1. SYN : "I want to connect"
2. SYN-ACK : "Confirmed, I hear you"
3. ACK : "Connection established"

**UDP (User Datagram Protocol)**
Connectionless. Sends data without verifying receipt.
Faster but no guarantee of delivery.

| Criteria | TCP | UDP |
|----------|-----|-----|
| Connection | Yes | No |
| Reliability | High | None |
| Speed | Slower | Faster |
| Error checking | Yes | No |
| Usage | Web, email, SSH, file transfer | Streaming, gaming, DNS, VoIP |

**Why packets get lost**
* Network congestion : routers drop packets when buffers are full
* Signal quality : weak WiFi or 4G generates transmission errors
* TTL expiry : a packet that traverses too many routers gets destroyed
* Equipment failure : a rebooting router drops packets in transit

**What happens when packets are lost**

With TCP : the sender waits for an ACK. If none arrives within the timeout,
the packet is retransmitted. TCP also slows down deliberately to reduce
congestion. Visible result : slower downloads, delayed page loads.

With UDP : lost packets are gone permanently. The application receives
what it receives and works with it. Visible result : pixelated video,
voice glitches, character teleporting in games.

**Port numbers**

| Port | Protocol | Service |
|------|----------|---------|
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |
| 22 | TCP | SSH |
| 21 | TCP | FTP |
| 25 | TCP | SMTP |
| 53 | UDP/TCP | DNS |
| 3389 | TCP | RDP |

**Security risks**
* SYN Flood : DDoS attack sending thousands of SYN packets without completing
the handshake, exhausting server resources
* Port scanning : attacker scans open ports to map exposed services
* UDP Flood : overwhelming a target with UDP packets

---

### Layer 5 : Session

Manages the opening, maintenance, and closing of communication sessions
between two devices. A session is established once a connection is
successfully made and remains active while the connection is live.

**Key features**
* Opens a session between two devices
* Keeps the session alive during data exchange
* Uses checkpoints : if data transfer is interrupted, only the data after
the last checkpoint needs to be retransmitted (not everything from scratch)
* Closes the session cleanly when done, or if the connection is idle too long
* Sessions are unique : data cannot travel across different sessions

**Example**
When you browse a website for 30 minutes, the session layer keeps your
connection coherent throughout. If your connection drops mid-download,
checkpoints mean the download can resume where it left off.

**Security risks**
* Session hijacking : stealing a session token to impersonate an authenticated user
* Session fixation : forcing a user to use a session ID already known to the attacker

---

### Layer 6 : Presentation

Handles data formatting, translation, compression, and encryption.
Ensures that data from the application layer of one system can be
understood by the application layer of another, even if they use
different formats.

**What it does**
* Encrypts and decrypts data (SSL/TLS)
* Compresses data to reduce size
* Translates between formats (ASCII, Unicode, JPEG, MP4)

**Example**
When you access an HTTPS site, the presentation layer manages TLS
encryption before data leaves your device.

**Security risks**
* SSL Stripping : downgrading an HTTPS connection to HTTP to intercept data
* Downgrade attacks : forcing a system to use an older, weaker version of a protocol
* Exploitation of vulnerabilities in file format parsers

---

### Layer 7 : Application

The layer the end user directly interacts with. Provides the protocols
and interfaces that applications use to communicate over the network.

**What it does**
Gives software access to network services : browsing the web, sending
email, transferring files.

**Protocols**

| Protocol | Usage |
|----------|-------|
| HTTP / HTTPS | Web browsing |
| FTP | File transfer |
| SMTP / IMAP / POP3 | Email |
| DNS | Domain name resolution |
| SSH | Secure remote access |

**Security risks**
* SQL Injection and XSS (Cross-Site Scripting)
* Phishing via email protocols
* DNS Spoofing
* API attacks

---

## Encapsulation

As data moves down the OSI layers from sender to receiver, each layer
adds its own header containing the information needed at that level.
This process is called encapsulation.

```
Layer 7 : [DATA]
Layer 6 : [DATA]
Layer 5 : [DATA]
Layer 4 : [TCP/UDP HEADER][DATA]          = Segment / Datagram
Layer 3 : [IP HEADER][SEGMENT]            = Packet
Layer 2 : [MAC HEADER][PACKET][FCS]       = Frame
Layer 1 : 010110100101...                 = Bits
```

On the receiving end, each layer reads and strips its header as data
moves back up the stack. This is called decapsulation.

**Data unit names by layer**

| Layer | Data unit |
|-------|-----------|
| 4 Transport | Segment (TCP) / Datagram (UDP) |
| 3 Network | Packet |
| 2 Data Link | Frame |
| 1 Physical | Bits |

---

## Questions answered

| Question | Answer |
|----------|--------|
| What is layer 4 called? | Transport |
| What does TCP stand for? | Transmission Control Protocol |
| What does UDP stand for? | User Datagram Protocol |
| What protocol guarantees accuracy of data? | TCP |
| What protocol does not care if data is received? | UDP |
| What protocol does an email client use? | TCP |
| What protocol does a file download application use? | TCP |
| What protocol does a video streaming application use? | UDP |
| What is layer 5 called? | Session |
| What is the technical term for a successfully established connection? | Session |

---

## Takeaway

The OSI model is not just a theoretical framework to memorise for exams.
It is a structured thinking tool that applies directly to how attacks work
and how defences are built.

Every major attack category maps to a specific layer : ARP Spoofing at
layer 2, IP Spoofing at layer 3, SYN Flood at layer 4, session hijacking
at layer 5, SSL stripping at layer 6, SQL injection at layer 7.

Understanding which layer a threat operates at tells you exactly which
control is needed to address it and where a gap in defences actually sits.

From a GRC perspective, this means an audit is not just checking whether
a firewall exists. It is verifying that controls exist at every relevant
layer for the organisation's threat model, and that no layer is left
exposed because it was assumed to be someone else's problem.

| Layer | Attack type | ISO 27001 control |
|-------|------------|-------------------|
| 7 Application | Injection, phishing, API attacks | A.8.19, A.8.23 |
| 6 Presentation | SSL stripping, downgrade | A.8.24 Cryptography |
| 5 Session | Session hijacking, fixation | A.8.23 Web filtering |
| 4 Transport | SYN Flood, port scanning | A.8.20 Network security |
| 3 Network | IP Spoofing, routing attacks | A.8.20 Network security |
| 2 Data Link | ARP Spoofing, MAC Flooding | A.8.22 Network segregation |
| 1 Physical | Wiretapping, physical destruction | A.7.8 Physical security |
