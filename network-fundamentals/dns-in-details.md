# DNS in Detail

**Platform** : TryHackMe
**Difficulty** : Easy
**Time** : 45 min
**Status** : Completed 100%
**Path** : Pre Security > How The Web Works

---

## Objective

Understand how DNS works, how domain names are structured, what record
types exist, and how a DNS request travels from a browser to an
authoritative server and back.

---

## Tasks covered

* Task 1 : What is DNS?
* Task 2 : Domain Hierarchy
* Task 3 : Record Types
* Task 4 : Making a Request
* Task 5 : Practical

---

## Task 1 : What is DNS?

DNS stands for Domain Name System.

It translates human-readable domain names like `tryhackme.com` into
IP addresses like `104.26.10.229` that machines use to communicate.

Without DNS, you would need to memorise the IP address of every website
you want to visit. DNS is the phonebook of the Internet.

It is a distributed system : no single server holds every domain.
Resolution is delegated across a hierarchy of servers, each responsible
for a portion of the namespace.

---

## Task 2 : Domain Hierarchy

A domain name is structured from right to left, from most general
to most specific.

```
subdomain . second-level-domain . TLD
   www    .      tryhackme      . com
```

### TLD (Top Level Domain)

The rightmost part of the domain name. Two categories exist.

**gTLD (generic Top Level Domain)**
Indicates the purpose or nature of the organisation.

| TLD | Usage |
|-----|-------|
| .com | Commercial |
| .org | Organisation |
| .edu | Education |
| .gov | Government |
| .net | Network |

**ccTLD (country code Top Level Domain)**
Indicates the country of origin.

| TLD | Country |
|-----|---------|
| .fr | France |
| .uk | United Kingdom |
| .de | Germany |
| .io | British Indian Ocean Territory (popular with tech startups) |

### Second Level Domain

The name directly to the left of the TLD.
In `tryhackme.com`, the second level domain is `tryhackme`.
Limited to 63 characters. Only letters, numbers, and hyphens allowed.

### Subdomain

Everything to the left of the second level domain.
In `admin.tryhackme.com`, `admin` is the subdomain.
Multiple levels are possible : `blog.admin.tryhackme.com`.
Each part is limited to 63 characters.
The total domain name length cannot exceed 253 characters.

---

## Task 3 : Record Types

DNS stores different types of records for each domain.
Each type serves a specific purpose.

### A Record

Resolves a domain name to an **IPv4** address.

```
tryhackme.com  →  A  →  104.26.10.229
```

### AAAA Record

Resolves a domain name to an **IPv6** address.

```
tryhackme.com  →  AAAA  →  2606:4700:20::681a:be5
```

Four times the bits of IPv4, hence four A's.

### CNAME Record (Canonical Name)

An alias that points one domain name to another domain name
rather than directly to an IP address.

```
store.tryhackme.com  →  CNAME  →  shops.shopify.com
shops.shopify.com    →  A      →  154.45.32.10
```

A second DNS request is then made to resolve the target domain.
Useful when multiple subdomains must point to the same service
without needing to update multiple A records if the IP changes.

### MX Record (Mail Exchange)

Points to the server responsible for handling emails for the domain.
Includes a priority flag : the server with the lowest value is tried first.
If the primary server is down, the next in priority order takes over.

```
tryhackme.com  →  MX priority 10  →  alt1.aspmx.l.google.com
tryhackme.com  →  MX priority 20  →  alt2.aspmx.l.google.com
```

### TXT Record

Stores free-form text data. Multiple uses in practice.

**SPF (Sender Policy Framework)**
Lists the servers authorised to send email on behalf of the domain.
Helps prevent email spoofing.
```
@ TXT "v=spf1 ip4:192.0.2.0/24 include:_spf.google.com ~all"
```

**DKIM (DomainKeys Identified Mail)**
Cryptographic signature on outgoing emails. Proves the email was not
tampered with in transit.

**DMARC**
Policy that tells receiving servers what to do with emails that fail
SPF or DKIM checks. `p=reject` means reject them entirely.
```
_dmarc.example.com TXT "v=DMARC1; p=reject; rua=mailto:reports@example.com"
```

**Domain ownership verification**
Used by third party services to confirm you own the domain.
```
@ TXT "MS=ms12345678"
_acme-challenge.example.com TXT "token_value_here"
```

SPF, DKIM and DMARC together form the email security triad.
An organisation without all three configured can have its domain
spoofed to send phishing emails that appear legitimate to recipients.

### TTL (Time To Live)

Every DNS record carries a TTL value expressed in seconds.
It tells resolvers and clients how long they can cache the record
before requesting it again.

| TTL | Effect |
|-----|--------|
| Short (300s) | Changes propagate quickly but generate more DNS traffic |
| Long (86400s) | Less traffic but slow propagation of urgent changes |

---

## Task 4 : Making a DNS Request

### The four key actors

**Recursive DNS Server**
Usually provided by your ISP but configurable (8.8.8.8 Google,
1.1.1.1 Cloudflare). Does the lookup work on your behalf.
Caches results to avoid repeating the same queries.

**Root DNS Server**
13 sets distributed worldwide (A through M).
Does not know the IP of any specific domain but knows which TLD
server handles each top level domain.

**TLD Server**
Manages all domains under a given TLD.
Does not know the IP of a specific domain but knows which
authoritative server is responsible for it.

**Authoritative Name Server**
The final server in the chain. Holds the actual DNS records for
the domain. All record updates are made here.
Often multiple authoritative servers exist per domain for redundancy.
For `tryhackme.com` : `kip.ns.cloudflare.com` and `uma.ns.cloudflare.com`.

### The full resolution process

```
1. You type tryhackme.com in your browser

2. Your computer checks its local DNS cache
   If found and TTL not expired → immediate response, process ends

3. If not cached, request goes to your Recursive DNS Server (ISP or 8.8.8.8)
   Recursive server checks its own cache
   If found → immediate response to your computer, process ends

4. If not cached, recursive server queries a Root Server
   "Who handles .com ?"
   Root server replies with the address of the .com TLD server

5. Recursive server queries the TLD Server for .com
   "Who is authoritative for tryhackme.com ?"
   TLD server replies with the authoritative nameserver address

6. Recursive server queries the Authoritative Name Server
   "What is the IP for tryhackme.com ?"
   Authoritative server replies with the A record : 104.26.10.229, TTL 300

7. Recursive server caches the result for 300 seconds
   and returns the IP to your computer

8. Your browser connects to 104.26.10.229
```

### Visualisation

```
BROWSER
   |
   | tryhackme.com ?
   v
RECURSIVE DNS SERVER (8.8.8.8)
   |                          ^
   | who handles .com ?       | IP : 104.26.10.229
   v                          |
ROOT SERVER → TLD .com → AUTHORITATIVE NAME SERVER
                              "104.26.10.229, TTL 300"
```

---

## DNS and Security

DNS was designed without security in mind. Traditional DNS queries
travel in plain text over UDP port 53. Several attacks exploit this.

### DNS Cache Poisoning

An attacker injects false records into a resolver's cache.
Users querying that resolver receive a fake IP and are redirected
to a malicious server, even when typing the correct URL.

Protection : DNSSEC (cryptographic signatures on DNS responses).

### DNS Hijacking

The attacker compromises the authoritative server or resolver directly
and modifies records at the source. Harder to detect because responses
appear to come from legitimate infrastructure.

### DNS Tunneling

Data is encoded inside DNS queries and responses to exfiltrate
information or establish a covert communication channel that bypasses
firewalls. DNS traffic is often allowed everywhere because it is
considered harmless.

```
Malicious query : 4d616c77617265446174610a.attacker.com
```

Protection : monitoring for abnormally long or frequent DNS queries,
DNS filtering (Cisco Umbrella, Cloudflare Gateway).

### DoH and DoT

Modern protocols that encrypt DNS queries to prevent interception.
* DoH (DNS over HTTPS) : DNS encapsulated in HTTPS on port 443
* DoT (DNS over TLS) : DNS over TLS on port 853

---

## Questions answered

| Question | Answer |
|----------|--------|
| What type of record advises where to send email ? | MX |
| What type of record handles IPv6 addresses ? | AAAA |
| What field specifies how long a DNS record should be cached for ? | TTL |
| What type of DNS server is usually provided by your ISP ? | Recursive DNS Server |
| What type of server holds all the records for a domain ? | Authoritative DNS Server |

---

## Takeaway

DNS is critical infrastructure. A DNS compromise can silently redirect
all users of an organisation to malicious servers without them noticing.

Every security professional needs to understand DNS because it sits
at the foundation of every network communication. Before any TCP
connection is established, a DNS lookup happens. Controlling or
manipulating DNS means controlling what users actually reach.

From a GRC perspective, DNS misconfiguration is one of the most
impactful and most overlooked risk areas in an audit.

| Risk | Attack | ISO 27001 Control |
|------|--------|-------------------|
| No SPF/DKIM/DMARC | Domain spoofing for phishing campaigns | A.8.20 Network security |
| Unsecured resolver | Cache poisoning, traffic redirection | A.8.20 Network security |
| DNS in plain text | Traffic interception and surveillance | A.8.24 Cryptography |
| DNS tunneling undetected | Covert data exfiltration | A.8.16 Monitoring |
| TTL too long on critical records | Slow propagation of emergency corrections | A.5.30 Business continuity |
| Single authoritative server | Full domain unavailability on failure | A.8.14 Redundancy |

Audit questions to ask :
* Are SPF, DKIM and DMARC configured on all company domains ?
* Is DNSSEC enabled on critical domains ?
* Is DNS traffic monitored for tunneling patterns ?
* Are internal DNS resolvers separated from public ones ?
* Are there redundant authoritative nameservers for every domain ?
* Are TTL values appropriate to allow rapid incident response ?
