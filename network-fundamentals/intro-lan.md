# Intro to LAN

**Platform** : TryHackMe
**Difficulty** : Easy
**Time** : 15 min
**Status** : Completed 100%
**Path** : Pre Security > Network Fundamentals

---

## Objective

Understand how local area networks are designed and built : topologies,
core networking devices, subnetting, ARP and DHCP.

---

## Tasks covered

* Task 1 : Introducing LAN Topologies
* Task 2 : A Primer on Subnetting
* Task 3 : ARP
* Task 4 : DHCP

---

## Key concepts

### LAN Topologies

A topology is the physical or logical layout of a network.
Three main types exist, each with different trade-offs.

**Star topology**
Every device connects individually to a central switch or hub.
Most common today. If one device fails, the rest keep working.
Single point of failure : if the central switch goes down, the whole network does too.
Expensive but highly scalable and easy to maintain.

**Bus topology**
All devices connect to a single backbone cable.
Cheap to set up but a single cable break kills the entire network.
Heavy traffic causes bottlenecks and collisions. Mostly obsolete.

**Ring topology**
Devices are connected in a loop. Data travels in one direction around the ring.
Easy to troubleshoot but slow, and any break in the ring takes everything down.

| Topology | Cost | Reliability | Scalability | Usage |
|----------|------|-------------|-------------|-------|
| Star | High | High | Excellent | Standard today |
| Bus | Low | Low | Poor | Obsolete |
| Ring | Medium | Medium | Poor | Rare |

---

### Switch vs Router

**Switch**
Connects devices within the same local network.
Intelligent : maintains a MAC address table and sends packets only to the correct destination port.
Far more efficient than a hub, which broadcasts to every port blindly.

**Router**
Connects different networks together.
Its job is called routing : finding the best path to send data from one network to another.
Routers can be chained together to create redundant paths. If one path fails, another takes over.

> Switch = inside a network. Router = between networks.

---

### Subnetting

Subnetting is dividing a large network into smaller sub-networks.

**Why it matters**
* Efficiency : traffic stays within its subnet and does not pollute other segments
* Security : a compromised segment does not automatically spread to the rest
* Control : the admin decides exactly who can talk to who

**The three address types in a subnet**

| Type | Role | Example |
|------|------|---------|
| Network address | Identifies the subnet itself. Not assignable to a device | 192.168.1.0 |
| Host address | Identifies a specific device on the network | 192.168.1.100 |
| Default gateway | Device responsible for sending data to other networks (router) | 192.168.1.254 |

**Subnet mask**
A 32-bit number (4 octets, range 0-255) that defines how many hosts fit in the subnet.

| Mask | CIDR | Available hosts |
|------|------|-----------------|
| 255.255.255.0 | /24 | 254 |
| 255.255.0.0 | /16 | 65 534 |

**Real world example**
A cafe runs two separate subnets : one for staff and cash registers, one for public WiFi.
Without subnetting, a malicious customer on the public network could reach internal systems.
With subnetting, both segments are completely isolated from each other.

---

### ARP (Address Resolution Protocol)

**The problem it solves**
On a local network, devices communicate using MAC addresses, not IP addresses.
When you want to send data to an IP, you need to find the corresponding MAC first. ARP does that translation.

**How it works**

1. ARP Request : your device broadcasts to the entire network "Who owns IP 192.168.1.50 ? Reply with your MAC."
2. ARP Reply : the device that owns that IP responds directly "That's me. My MAC is a4:c3:f0:85:ac:2d."
3. ARP Cache : your device stores that IP/MAC mapping locally to avoid asking again.

**Security risk : ARP Spoofing**
An attacker sends fake ARP replies without being asked, claiming that their MAC corresponds to the router's IP.
All devices update their ARP cache with false information and start sending traffic to the attacker.
The attacker sits in the middle of all communications without anyone noticing.
This is a Man-in-the-Middle attack.

Protection : Dynamic ARP Inspection (DAI) on enterprise switches.

---

### DHCP (Dynamic Host Configuration Protocol)

**The problem it solves**
Every device joining a network needs an IP address. DHCP assigns them automatically.
Without it, every device would need to be manually configured.

**The DORA process**

| Step | Packet | Description |
|------|--------|-------------|
| D | DHCP Discover | Device broadcasts : "Is there a DHCP server here ?" |
| O | DHCP Offer | Server replies : "Yes, I offer you IP 192.168.1.105" |
| R | DHCP Request | Device accepts : "I'll take that IP, please confirm" |
| A | DHCP ACK | Server confirms : "Done. That IP is yours for the lease duration" |

**DHCP Lease**
The assigned IP is temporary. Once the lease expires, the IP can be reassigned.
This is why your IP can change between connections.

**Security risks**
* DHCP Starvation : attacker floods the server with fake requests to exhaust all available IPs. Legitimate devices can no longer connect.
* Rogue DHCP : attacker runs a fake DHCP server that responds before the real one and assigns a false default gateway pointing to the attacker. Another Man-in-the-Middle setup.

---

## Questions answered

| Question | Answer |
|----------|--------|
| What does LAN stand for ? | Local Area Network |
| What verb describes what routers do ? | Routing |
| What device centrally connects multiple devices on a LAN ? | Switch |
| What topology is the cheapest to set up ? | Bus topology |
| What topology is the most expensive to set up and maintain ? | Star topology |
| What is the technical term for dividing a network into smaller pieces ? | Subnetting |
| How many bits are in a subnet mask ? | 32 |
| What is the range of an octet in a subnet mask ? | 0-255 |
| What address identifies the start of a network ? | Network address |
| What address identifies devices within a network ? | Host address |
| What device is responsible for sending data to another network ? | Default gateway |
| What does ARP stand for ? | Address Resolution Protocol |
| What ARP packet asks if a device has a specific IP ? | ARP Request |
| What address is the physical identifier of a device ? | MAC address |
| What address is the logical identifier of a device ? | IP address |
| What DHCP packet retrieves an IP address ? | DHCP Discover |
| What DHCP packet does a device send after receiving an offer ? | DHCP Request |
| What is the last DHCP packet sent to a device ? | DHCP ACK |

---

## Takeaway

This room connects four foundational concepts that operate together on every local network.

Topology defines the physical structure and its failure points.
The switch and router define how data moves inside and between networks.
Subnetting defines the security boundaries within a network.
ARP and DHCP are the protocols that make addressing work automatically.

From a GRC perspective, each of these has a direct risk implication.
An unsegmented network, a missing DAI policy, or an unsecured DHCP server
are not just technical gaps : they are control failures that map directly
to ISO 27001 A.8.20 (network security) and A.8.22 (network segregation),
and to NIS2 risk management obligations.

Understanding the technical layer makes it possible to assess whether
an organisation's controls actually address the real attack surface,
not just whether the right boxes are ticked on paper.
