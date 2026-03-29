# Packets & Frames

**Platform** : TryHackMe
**Difficulty** : Easy
**Time** : 30 min
**Status** : Completed 100%
**Path** : Pre Security > Network Fundamentals

---

## Objective

Understand how data is divided into smaller pieces and transmitted across
a network. Cover TCP/IP, the three-way handshake, UDP, and ports.

---

## Tasks covered

* Task 1 : What are Packets and Frames
* Task 2 : TCP/IP (The Three-Way Handshake)
* Task 3 : Practical - Handshake
* Task 4 : UDP/IP
* Task 5 : Ports 101 (Practical)

---

## Task 1 : What are Packets and Frames

Data sent across a network is not transmitted as one large block.
It is broken down into smaller units called packets and frames.

**Frame (Layer 2)**
Operates at the Data Link layer. Contains MAC addresses.
Handles communication between two directly connected devices on the same
local network. Does not survive past a router : at each hop, the frame
is stripped and a new one is created for the next segment.

**Packet (Layer 3)**
Operates at the Network layer. Contains IP addresses.
Travels across different networks from source to final destination.
The packet stays the same end to end while frames change at each hop.

**The analogy**
A packet is the parcel with the final delivery address.
A frame is the delivery truck that carries it between two warehouses.
At each warehouse (router), the parcel changes truck but stays the same parcel.

---

## Task 2 : TCP/IP and the Three-Way Handshake

### The TCP/IP model

TCP/IP is a simplified version of OSI with 4 layers.

| TCP/IP Layer | Equivalent OSI Layers |
|--------------|----------------------|
| Application | 7, 6, 5 |
| Transport | 4 |
| Internet | 3 |
| Network Interface | 2, 1 |

Encapsulation and decapsulation work the same way as in OSI :
each layer adds its header going down, strips it going back up.

---

### TCP headers

Every TCP packet contains headers that carry essential control information.

| Header | Description |
|--------|-------------|
| Source Port | Randomly chosen port opened by the sender (0 to 65535) |
| Destination Port | Port of the target service on the remote host (not random) |
| Source IP | IP address of the sending device |
| Destination IP | IP address of the destination device |
| Sequence Number | Random initial number assigned to the first piece of data sent |
| Acknowledgement Number | Sequence number + 1, confirming the next expected piece of data |
| Checksum | Mathematical value ensuring data integrity. If the recalculated value differs at reception, the packet is corrupted and rejected |
| Data | The actual content being transmitted (bytes of a file, etc.) |
| Flag | Controls the behaviour of the packet during the handshake and connection lifecycle |

---

### TCP Flags

Flags are bits in the TCP header that activate specific behaviours.
Multiple flags can be active simultaneously.

| Flag | Name | Role |
|------|------|------|
| SYN | Synchronize | Opens a connection and synchronises sequence numbers. Only used during the initial handshake |
| ACK | Acknowledgement | Confirms receipt of a packet. Present in almost every packet after the first SYN |
| FIN | Finish | Signals the sender has finished transmitting. Initiates a clean connection closure |
| RST | Reset | Abruptly terminates a connection without negotiation. Used when something goes wrong |
| PSH | Push | Tells the Transport layer to deliver data to the application immediately without buffering |
| URG | Urgent | Marks certain data as high priority. Rarely used in practice |

**RST vs FIN**
FIN is a clean, polite goodbye. Both sides negotiate the closure.
RST is hanging up the phone mid-sentence. Immediate, no negotiation.

In traffic analysis : RST in response to SYN = port is closed.
No response to SYN at all = port is filtered by a firewall.
This is exactly what Nmap reads during a port scan.

---

### Sequence Numbers

Sequence numbers are the core mechanism that makes TCP reliable.

At the start of a connection, both devices choose a random Initial
Sequence Number (ISN). Random to prevent TCP Sequence Prediction Attacks
where an attacker could inject fake packets into an existing connection.

```
Client ISN = 0     Server ISN = 5000

Client  SYN      seq=0
Server  SYN/ACK  seq=5000  ack=1      "Send me packet 1 next"
Client  ACK      seq=1     ack=5001   "Send me packet 5001 next"
```

Each DATA packet increments the sequence number by 1.
The receiver always ACKs with the next expected number.
If a packet is lost, the receiver keeps requesting the same number.
The sender understands it must retransmit.

| Device | Initial Sequence | Next Sequence |
|--------|-----------------|---------------|
| Client | 0 | 0 + 1 = 1 |
| Client | 1 | 1 + 1 = 2 |
| Client | 2 | 2 + 1 = 3 |

---

### The Three-Way Handshake

```
CLIENT                              SERVER

SYN ──────────────────────────────>
"I want to connect.
 My ISN is 0."

<────────────────────────────── SYN/ACK
                                    "Confirmed. My ISN is 5000.
                                     I acknowledge your ISN (0)."

ACK ──────────────────────────────>
"I acknowledge your ISN (5000).
 Connection established."

DATA ─────────────────────────────>
<──────────────────────────────── DATA
```

Three steps and not two because both sides need to confirm they can
both send AND receive before any real data transfer begins.

---

### Closing a TCP Connection (Four-Way Handshake)

TCP is full-duplex : both sides send and receive on independent channels.
Closing requires each side to close its own channel independently.

```
Alice                               Bob

FIN ──────────────────────────────>
"I am done sending."

<────────────────────────────── ACK
                                    "Received. I may still
                                     have data to send though."

                                    ... Bob sends remaining data ...

<────────────────────────────── FIN
                                    "I am done sending too."

ACK ──────────────────────────────>
"Received. Connection fully closed."
```

---

### TCP advantages and disadvantages

| Advantages | Disadvantages |
|------------|---------------|
| Guarantees data integrity | Requires reliable connection. If one chunk is lost, the entire chunk must be resent |
| Synchronises both devices to prevent data flooding | Slow connections bottleneck the other device as the connection is reserved |
| Error checking built in | Significantly slower than UDP due to extra processing |

---

## Task 4 : UDP/IP

UDP has no handshake, no sequence numbers, no acknowledgements.
A datagram is sent and that is it. No verification of receipt.

### UDP header structure

| Field | Size | Role |
|-------|------|------|
| Source Port | 2 bytes | Port of the sending application |
| Destination Port | 2 bytes | Port of the receiving application |
| Length | 2 bytes | Total size of the datagram |
| Checksum | 2 bytes | Error detection (optional in IPv4) |
| Data | Variable | Content to transmit |

8 bytes total vs 20 bytes minimum for TCP. That is why UDP is faster.

### What happens when UDP packets are lost

Nothing at the protocol level. The application receives what it receives
and works with it. Lost packets are gone permanently.

Visible results : pixelated video, voice glitches on VoIP calls,
character teleporting in games.

### When to use TCP vs UDP

| Use TCP when | Use UDP when |
|-------------|-------------|
| Every byte must arrive intact | Speed matters more than perfection |
| File transfers | Video streaming |
| Web browsing (HTTP/HTTPS) | Online gaming |
| Email | DNS lookups |
| SSH | VoIP calls |

---

## Task 5 : Ports

A port is a numbered entry point on a device. Every simultaneous network
conversation uses a different port number so the OS knows which application
to deliver each packet to.

Port numbers range from 0 to 65535.
Ports 0 to 1024 are called common ports and are reserved for standard services.

### Common ports to know

| Port | Protocol | Description |
|------|----------|-------------|
| 21 | FTP | File transfer |
| 22 | SSH | Secure remote command-line access |
| 80 | HTTP | Unencrypted web traffic |
| 443 | HTTPS | Encrypted web traffic |
| 445 | SMB | Windows file and printer sharing |
| 3389 | RDP | Windows remote desktop |

### Non-standard ports

A service can run on any port. A web server can run on port 8080
instead of 80. The browser will not assume this automatically so
you must specify it explicitly : `http://example.com:8080`

This is sometimes done to reduce automated scan noise but it does
not constitute real security. A thorough port scan finds it anyway.

### Ports and security

An open port is a potential attack surface.
When an attacker runs Nmap, they map open ports to identify exposed services
and look for known vulnerabilities on each one.

* RDP open on the Internet (3389) : classic target for brute force attacks
* SMB open (445) : the vector exploited by EternalBlue and WannaCry
* FTP open (21) : often misconfigured with anonymous access enabled

---

## Questions answered

| Question | Answer |
|----------|--------|
| What header ensures integrity of data in TCP? | Checksum |
| Order of a normal Three-way handshake? | SYN, SYN/ACK, ACK |

---

## Takeaway

Packets and frames are the foundation of everything that happens on a network.
Every attack, every detection, every forensic analysis starts here.

TCP gives you reliability through sequence numbers, checksums and handshakes.
UDP gives you speed at the cost of guaranteed delivery.
Ports tell you which service you are talking to and where to look for weaknesses.

From a GRC perspective, this translates directly into audit questions.

* Which ports are open and exposed on the perimeter ?
* Is RDP accessible from the Internet without a VPN ?
* Is SMB filtered between network segments ?
* Are non-standard ports documented and justified ?
* Is UDP traffic monitored given the absence of built-in reliability checks ?

A port scan report from a penetration test is meaningless to someone who
does not understand what each open port represents. Understanding TCP, UDP
and ports is what lets a GRC consultant read that report, assess the real
risk behind each finding, and prioritise remediation without needing an
engineer to translate every line.

| Risk | Attack | ISO 27001 Control |
|------|--------|-------------------|
| Open RDP on Internet | Brute force, ransomware entry | A.8.20 Network security |
| SMB exposed between segments | Lateral movement, WannaCry | A.8.22 Network segregation |
| SYN Flood | DDoS on Transport layer | A.8.20 Network security |
| Undocumented open ports | Unknown attack surface | A.8.8 Vulnerability management |
| FTP in clear text | Credential interception | A.8.24 Cryptography |
