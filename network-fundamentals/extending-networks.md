# Extending Your Network

**Platform** : TryHackMe
**Difficulty** : Easy
**Time** : 20 min
**Status** : Completed 100%
**Path** : Pre Security > Network Fundamentals

---

## Objective

Understand the technologies used to extend a private network onto the
Internet : port forwarding, firewalls, VPNs, and core LAN devices.

---

## Task 1 : Introduction to Port Forwarding

### Context : why port forwarding exists

NAT hides all devices on a private network behind a single public IP.
Every device on the LAN has a private IP (192.168.x.x, 10.x.x.x)
that is invisible from the Internet. This protects internal devices
from direct external access.

The problem : if you want to host a service accessible from the Internet
(a web server, a game server, a camera feed), external users cannot
reach the internal device directly because they only see the public IP
of the router.

Port forwarding solves this by instructing the router to redirect
incoming connections on a specific port to a specific internal device.

### How it works

```
INTERNET                      ROUTER                    LOCAL NETWORK

User connects to     ──>   Port 80 incoming         ──>  192.168.1.50:80
86.157.52.21:80            redirected to                  (internal web server)
                           192.168.1.50:80
```

The forwarding rule says :
"Everything arriving on my public port 80 goes to 192.168.1.50 port 80."

From the external user's perspective, they reach the web server at the
router's public IP. They have no visibility into the internal network.

### Common port forwarding use cases

* Hosting a web server internally accessible from the Internet
* Running a game server reachable by external players
* Accessing a home security camera remotely
* Exposing a self-hosted service (NAS, media server, etc.)

### Security implications

Every port forwarding rule is a deliberate hole in the perimeter.
An exposed service on the Internet is a target.

Key risks :
* Forgotten rules exposing services that are no longer maintained
* Outdated software running behind an open port with known CVEs
* No authentication on exposed services
* Services designed for internal use only accidentally exposed

Audit questions :
* Which ports are forwarded inward and to which internal hosts ?
* Are all forwarded services up to date and correctly configured ?
* Are there legacy rules that no longer serve a business purpose ?
* Is there a firewall rule limiting who can reach forwarded ports ?

---

## Task 2 : Firewalls 101

### What is a firewall

A firewall is a device or software responsible for determining what
traffic is allowed to enter and exit a network. Think of it as border
security : it inspects packets and decides whether to let them through
or block them based on configured rules.

A firewall filters traffic based on :
* Where the traffic is coming from (source IP or network)
* Where it is going to (destination IP or network)
* What port it targets (port 80, port 443, etc.)
* What protocol it uses (TCP, UDP, or both)

Firewalls exist in multiple forms :
* Dedicated hardware appliances for enterprise networks
* Software running on a server or endpoint (like Snort)
* Built into residential routers

### The two primary categories

**Stateful firewall**

Tracks the state of every active connection passing through it.
Does not inspect packets individually but evaluates the behaviour
of a device across the entire connection lifecycle.

How it works in practice :
* It allows the first parts of a TCP handshake that could later turn malicious
* If a connection from a host is identified as bad, it blocks the entire device
* It understands context : an ACK packet arriving without a prior SYN
  from that host is suspicious and can be blocked

More intelligent but consumes significantly more resources because it
must maintain a state table of all active connections.

**Stateless firewall**

Applies a fixed set of rules to each individual packet in isolation.
Has no memory of previous packets. Every packet is evaluated fresh
against the ruleset.

How it works in practice :
* A device sending one bad packet is not necessarily blocked entirely
* If a packet matches no rule exactly, the firewall has no response
* Rules must be extremely precise to be effective

Uses far fewer resources than stateful firewalls.
Effective against large-volume, predictable attacks like DDoS
from a defined set of source hosts. Less effective against
sophisticated multi-packet attacks that exploit connection context.

| Criteria | Stateful | Stateless |
|----------|----------|-----------|
| Inspection scope | Entire connection | Individual packets |
| Resource usage | High | Low |
| Intelligence | High | Low |
| Context awareness | Yes | No |
| Blocks entire device on bad behaviour | Yes | No |
| Best use case | Sophisticated threat filtering | High-volume DDoS mitigation |

### OSI layers

Firewalls operate at layers 3 and 4.

* Layer 3 (Network) : inspects source and destination IP addresses
* Layer 4 (Transport) : inspects TCP/UDP ports and connection state

A stateful firewall additionally tracks the TCP handshake state,
which is a layer 4 function. Next Generation Firewalls (NGFW) extend
inspection up to layer 7 (Application) via Deep Packet Inspection.

### Drop vs Reject

**Drop** : the packet is silently discarded. The sender receives no
response and cannot determine whether a firewall blocked the packet
or the host simply does not exist. Preferred in security because
it leaks no information about the perimeter.

**Reject** : the packet is blocked but the sender receives an ICMP
"destination unreachable" message. Confirms to an attacker that
something exists behind the firewall. Avoidable in most configurations.

### Firewall rule structure

Each rule defines an action applied to traffic matching specific criteria.

| Field | Description |
|-------|-------------|
| Source IP | Where the connection originates |
| Destination IP | Where the connection is headed |
| Source port | Sending application port |
| Destination port | Target service port |
| Protocol | TCP, UDP, ICMP |
| Action | Accept, Drop, or Reject |

Rules are evaluated top to bottom. The first matching rule wins.
A default deny rule at the bottom blocks everything not explicitly allowed.

### Firewall zones

Enterprise firewalls segment the network into trust zones.

**Internet (untrusted)**
All inbound traffic from the Internet. Zero assumed trust.

**DMZ (DeMilitarized Zone)**
Intermediate zone hosting servers that must be reachable from the
Internet : web servers, mail servers, public DNS.
Isolated from the internal LAN. If a DMZ server is compromised,
the attacker cannot directly pivot to internal systems.

**Internal LAN (trusted)**
Workstations, internal servers, sensitive data. Never directly
reachable from the Internet.

```
INTERNET ──> [PERIMETER FIREWALL] ──> DMZ (web, mail, DNS)
                                   ──> [INTERNAL FIREWALL] ──> LAN
```

---

## Task 3 : Practical - Firewall

The practical simulates configuring firewall rules to allow or deny
specific traffic between network zones.

Key takeaway from the practical :
Rule order matters. A permissive rule placed above a deny rule will
allow traffic before the deny rule is ever evaluated.
Default deny at the bottom is the correct posture : block everything
not explicitly permitted.

---

## Task 4 : VPN Basics

### What is a VPN

A VPN (Virtual Private Network) creates an encrypted tunnel between
two endpoints across the Internet. Devices connected via VPN communicate
as if they were physically on the same local network, regardless of
their actual geographic location.

### How encapsulation works

The VPN wraps the original packet inside a new encrypted packet.

```
ORIGINAL PACKET
[src: 192.168.1.10][dst: 10.10.10.50][DATA]

VPN ENCAPSULATED PACKET
[src: 86.x.x.x][dst: VPN_SERVER][ENCRYPTED[192.168.1.10 > 10.10.10.50][DATA]]
```

An observer on the Internet sees only the outer packet between
the user's public IP and the VPN server.
The real content and final destination are invisible.

### Use cases

**Remote access VPN**
An employee working remotely connects to the company VPN.
Their device virtually joins the internal network.
They access internal servers, file shares, and applications
as if physically at the office. All traffic is encrypted
between their device and the VPN server.

**Site-to-site VPN**
Two office locations in different cities are linked via VPN.
Both LANs communicate securely through the Internet
as one unified network. No user configuration required,
the routers handle the tunnel automatically.

**Traffic confidentiality**
Encrypts traffic on untrusted networks like public WiFi
to prevent interception by other users on the same network.

### Common VPN protocols

| Protocol | Notes |
|----------|-------|
| OpenVPN | Open source, highly secure, very flexible. Enterprise standard |
| WireGuard | Modern, very fast, minimal codebase means smaller attack surface |
| IPSec | Robust and widely supported. Standard for site-to-site VPNs |
| L2TP/IPSec | Combines layer 2 tunneling with IPSec. Less performant than modern alternatives |
| PPTP | Old protocol with known vulnerabilities. Never use |

### VPN and Zero Trust

Traditional VPN assumption : once connected, the user is trusted
and can access everything on the internal network.
If VPN credentials are compromised, the attacker inherits that broad access.

Zero Trust challenges this model : even after connecting via VPN,
every access to every individual resource must be separately
authenticated and authorised based on identity, device posture,
and context. No implicit trust based on network location.

This is the direction modern enterprise security architecture is taking,
particularly relevant under NIS2 and DORA requirements for access control.

---

## Task 5 : LAN Networking Devices

### Router

Connects different networks together and routes packets between them.
Operates at OSI layer 3 (Network layer).
Reads IP addresses to determine the best path for each packet.

The router maintains a routing table : a map of known networks and
the optimal path to reach each one. When a packet arrives, the router
consults the table and forwards accordingly.

Routers can be chained to create redundant paths. If one path fails,
traffic reroutes automatically with minimal disruption.

The router is the device that links your local network to the Internet.
It is also the device that performs NAT, translating private IPs to
the single public IP assigned by the ISP.

### Switch

Connects multiple devices within the same local network.
Operates at OSI layer 2 (Data Link layer).
Maintains a MAC address table : a record of which MAC address
is connected to which physical port.

When a frame arrives, the switch reads the destination MAC address,
looks it up in its table, and sends the frame only to the correct port.
This is fundamentally different from a hub, which blindly forwards
every frame to every port regardless of destination.

A switch that receives a frame for an unknown MAC address will
broadcast it to all ports (flooding) until it learns the mapping.
This is a normal behaviour but can be exploited via MAC Flooding attacks
to overwhelm the MAC table and force the switch into hub mode,
making all traffic visible to an attacker.

**Layer 3 switch**
Advanced switches can also perform routing at layer 3.
They combine switching and routing in a single device.
Common in enterprise networks for inter-VLAN routing.

### Hub (legacy)

Broadcasts every frame it receives to every connected port.
Has no MAC table and no ability to direct traffic intelligently.
Every device on the hub sees every frame, even those not addressed to it.
Creates collision domains and generates unnecessary traffic.
Entirely obsolete in modern networks. Replaced by switches everywhere.

Security risk : a device connected to a hub can passively capture
all traffic on the segment with a packet analyser like Wireshark
without any special configuration needed.

### VLAN (Virtual LAN)

A VLAN logically segments a physical switch into multiple isolated
virtual networks. Devices on different VLANs cannot communicate
directly even if they share the same physical switch.

Use cases :
* Separating employee and guest WiFi on the same infrastructure
* Isolating IoT devices from workstations
* Segmenting finance, HR, and IT on the same physical network

VLANs enforce segmentation at layer 2 without requiring separate
physical hardware for each segment.

### Key comparison

| Device | OSI Layer | Addressing | Behaviour |
|--------|-----------|------------|-----------|
| Hub | 1 | None | Broadcasts to all ports |
| Switch | 2 | MAC address | Forwards to correct port only |
| Router | 3 | IP address | Routes between networks |
| Layer 3 switch | 2 and 3 | MAC and IP | Switches and routes |

---

## Task 6 : Practical - Network Simulator

The practical simulates a full network with multiple devices,
a switch, a router, and Internet connectivity.

Key observations from the simulator :
* Packets travel from device to switch via MAC addressing (layer 2)
* The router forwards packets between the LAN and the Internet via IP (layer 3)
* Each hop adds or removes encapsulation headers
* Firewall rules on the router control what traffic exits and enters

---

## Questions answered

| Question | Answer |
|----------|--------|
| What OSI layers do firewalls operate at ? | 3 & 4 |
| What category of firewall inspects the entire connection ? | Stateful |
| What category of firewall inspects individual packets ? | Stateless |

---

## Takeaway

This room covers the technologies that define the boundary between
a private network and the outside world.

Port forwarding controls what external connections can reach internally.
Firewalls control what traffic is allowed to pass in both directions.
VPNs create secure tunnels for remote access and site interconnection.
Routers and switches define how traffic moves at every layer.

These technologies form the perimeter and internal architecture that
any security assessment must systematically evaluate. Weaknesses here
are typically the most critical audit findings because they represent
the outermost layer of the defence stack.

| Risk | Threat | ISO 27001 Control |
|------|--------|-------------------|
| No DMZ | Compromised public server pivots directly to internal LAN | A.8.22 Network segregation |
| Stateless firewall on complex perimeter | Sophisticated bypass via multi-packet attacks | A.8.20 Network security |
| VPN without MFA | Stolen credentials give full internal access | A.8.5 Authentication |
| Forgotten port forwarding rules | Unmaintained services exposed on Internet | A.8.8 Vulnerability management |
| PPTP or weak VPN protocol | Known protocol vulnerabilities exploitable | A.8.24 Cryptography |
| Hubs remaining on network | Passive traffic capture with no detection possible | A.8.20 Network security |
| No VLAN segmentation | Flat network allows lateral movement across all segments | A.8.22 Network segregation |

Systematic audit questions :
* Is there a DMZ separating public-facing servers from the internal LAN ?
* Are firewall rules documented, business-justified, and reviewed regularly ?
* Does VPN access enforce MFA on every authentication ?
* Are all port forwarding rules inventoried and still necessary ?
* Is the VPN protocol modern and free of known vulnerabilities ?
* Are hubs still present anywhere on the network ?
* Is VLAN segmentation applied to isolate sensitive systems and guest access ?
