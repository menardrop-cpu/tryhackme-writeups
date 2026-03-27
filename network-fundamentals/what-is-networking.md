# What is Networking?

**Platform** : TryHackMe
**Difficulty** : Easy
**Time** : 30 min
**Status** : Completed 100%

## Objective

Understand the fundamentals of computer networking : how devices are
identified, how they communicate, and what the Internet actually is.

## Tasks covered

* Task 1 : What is Networking?
* Task 2 : What is the Internet?
* Task 3 : Identifying Devices on a Network
* Task 4 : Ping (ICMP)

## Key concepts

**IP Address**
A numerical label assigned to each device on a network. It can change
over time and is split into four octets. Two types exist : public (used
on the Internet, assigned by the ISP) and private (used within a local
network).

* IPv4 : 2^32 addresses (~4.29 billion), increasingly exhausted
* IPv6 : 2^128 addresses (~340 trillion), more efficient, built to replace IPv4

**MAC Address**
A permanent physical identifier assigned to a network interface at the
factory. Twelve hexadecimal characters split by colons. The first six
identify the manufacturer, the last six are unique to the device.

Unlike an IP address, a MAC address does not change. However it can be
spoofed, which allows a device to impersonate another on the network and
bypass MAC-based access controls.

## Practical : MAC spoofing

Simulated a hotel Wi-Fi scenario where only paying users (Alice) had
network access. Changed Bob's MAC address to match Alice's to bypass
the router filtering.


## Takeaway

Two layers of identity coexist on a network : the logical (IP, flexible)
and the physical (MAC, permanent but spoofable). Security controls that
rely solely on MAC filtering are weak by design. Understanding this
distinction matters in GRC when assessing whether network access controls
are actually robust or just creating an illusion of security.
