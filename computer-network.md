# Computer Networks — Interview & Exam Notes

---

## 1. Introduction / Overview

A **Computer Network** is a collection of interconnected devices that share resources and information. Networks are fundamental to the internet, web applications, cloud computing, and communication systems.

**Key Terminology:**
- **Node:** Any device on the network (PC, router, switch, server)
- **Host:** End device (client or server)
- **Link:** Physical or logical connection between nodes
- **Bandwidth:** Maximum data rate of a link (bps)
- **Latency:** Time for data to travel from source to destination
- **Throughput:** Actual data rate achieved

---

## 2. Network Classifications

### By Scale

| Type | Scale          | Example                     |
|------|----------------|-----------------------------|
| PAN  | Personal (~10m)| Bluetooth, USB              |
| LAN  | Local (<1km)   | Office network, home WiFi   |
| MAN  | City (~100km)  | Cable TV, metro Ethernet    |
| WAN  | Worldwide      | Internet, MPLS networks     |

### By Topology

```
Bus:  [ Node ]---[ Node ]---[ Node ]---[ Node ]
      Single backbone; easy to install; single point of failure

Star:          [ Hub/Switch ]
              /      |      \
       [Node] [Node] [Node] [Node]
      Centralized; easy to add nodes; hub = single point of failure

Ring: [ Node ] → [ Node ] → [ Node ] → [ Node ] → (back to start)
      Token passing; equal access; one failure breaks ring

Mesh: Every node connected to every other node
      Highly redundant; expensive; used in WANs
      Full mesh: n(n-1)/2 links
```

---

## 3. OSI Model

> The **Open Systems Interconnection** model is a conceptual framework with 7 layers for understanding how data travels from one device to another.

```
┌─────────────────────────────────────────────────────────┐
│  7. Application Layer  │ HTTP, FTP, SMTP, DNS, SNMP     │
│  6. Presentation Layer │ SSL/TLS, JPEG, MPEG, ASCII      │
│  5. Session Layer      │ NetBIOS, RPC, PPTP              │
│  4. Transport Layer    │ TCP, UDP, ports                 │
│  3. Network Layer      │ IP, ICMP, ARP, routers          │
│  2. Data Link Layer    │ Ethernet, MAC, switches         │
│  1. Physical Layer     │ Cables, hubs, bits              │
└─────────────────────────────────────────────────────────┘
```

**Mnemonic (top → bottom):** **A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing

| Layer | Name         | PDU    | Key Functions                                      | Devices       |
|-------|--------------|--------|----------------------------------------------------|---------------|
| 7     | Application  | Data   | User interface, HTTP, email, DNS                  | Gateways      |
| 6     | Presentation | Data   | Encryption, compression, data format conversion   | —             |
| 5     | Session      | Data   | Session establishment, maintenance, termination   | —             |
| 4     | Transport    | Segment| End-to-end delivery, error control, flow control  | —             |
| 3     | Network      | Packet | Logical addressing (IP), routing                  | Routers       |
| 2     | Data Link    | Frame  | Physical addressing (MAC), error detection        | Switches, bridges |
| 1     | Physical     | Bit    | Bit transmission, physical medium                 | Hubs, repeaters |

---

## 4. TCP/IP Model

> A practical 4-layer model used in the real internet.

```
┌────────────────────────────────────────────────────┐
│  4. Application Layer  (OSI layers 5, 6, 7)        │
│  3. Transport Layer    (OSI layer 4)               │
│  2. Internet Layer     (OSI layer 3)               │
│  1. Network Access     (OSI layers 1, 2)           │
└────────────────────────────────────────────────────┘
```

### OSI vs TCP/IP

| Feature              | OSI Model               | TCP/IP Model               |
|----------------------|-------------------------|----------------------------|
| Layers               | 7                       | 4                          |
| Developed by         | ISO                     | DARPA                      |
| Nature               | Conceptual/theoretical  | Practical/implementation   |
| Protocol-independence| Yes                     | No (TCP/IP specific)       |
| Session/Presentation | Separate layers         | Part of Application layer  |

---

## 5. Physical Layer

- **Transmission media:**
  - **Guided:** Twisted pair (Cat5/6), Coaxial cable, Fiber optic
  - **Unguided:** Radio waves, Microwave, Infrared

- **Bandwidth vs Throughput:** Bandwidth is theoretical max; throughput is actual.

- **Nyquist Theorem (noiseless):** `Max bit rate = 2B log₂V`  
  where B = bandwidth (Hz), V = signal levels

- **Shannon's Theorem (noisy):** `Max bit rate = B log₂(1 + S/N)`  
  where S/N = Signal-to-Noise ratio

---

## 6. Data Link Layer

### 6.1 Functions
- Framing, physical addressing (MAC), error detection/correction, flow control, access control

### 6.2 Error Detection Methods

| Method          | How it works                                | Notes                          |
|-----------------|---------------------------------------------|--------------------------------|
| Parity bit      | Add bit to make total 1s even/odd           | Detects odd-number errors      |
| Checksum        | Sum of data segments in 1s complement       | Used in TCP/UDP/IP             |
| CRC             | Polynomial division of data                 | Most powerful; used in Ethernet|

### 6.3 Error Correction — Hamming Code
- Can detect up to 2-bit errors and **correct 1-bit errors**
- Redundant bits `r` needed for `m` data bits: `2^r ≥ m + r + 1`

### 6.4 Flow Control Protocols

| Protocol      | Description                                       |
|---------------|---------------------------------------------------|
| Stop-and-Wait | Send one frame, wait for ACK                      |
| Go-Back-N (GBN)| Send N frames without waiting; on error, retransmit from error frame |
| Selective Repeat (SR) | Retransmit only the erroneous frame       |

**Sliding Window:**
- Window size = `2^n - 1` for GBN (sender), `2^(n-1)` for SR (both sender & receiver)

### 6.5 MAC Protocols (Multiple Access)

| Category | Protocol | Notes                               |
|----------|----------|-------------------------------------|
| Random Access | ALOHA | Pure ALOHA: 18.4%; Slotted: 36.8% efficiency |
| Random Access | CSMA/CD | Carrier Sense Multiple Access with Collision Detection; used in Ethernet |
| Random Access | CSMA/CA | Collision Avoidance; used in WiFi (802.11) |
| Controlled | Token Ring | Token passed around; holder transmits |

### 6.6 Ethernet
- IEEE 802.3
- Uses CSMA/CD
- MAC address: 48-bit (6 bytes), e.g., `AA:BB:CC:DD:EE:FF`

### 6.7 Devices

| Device   | Layer | Description                                      |
|----------|-------|--------------------------------------------------|
| Hub      | 1     | Broadcasts to all ports; no intelligence         |
| Switch   | 2     | Forwards based on MAC address table              |
| Router   | 3     | Routes based on IP address                       |
| Gateway  | 7     | Protocol translation between different networks  |
| Bridge   | 2     | Connects two LANs at data link layer             |
| Repeater | 1     | Amplifies/regenerates signal                     |

---

## 7. Network Layer

### 7.1 IP Addressing

#### IPv4
- 32-bit address, dotted decimal notation: `192.168.1.1`
- **Classes:**

| Class | First Octet | Default Mask   | Hosts per Network |
|-------|-------------|----------------|-------------------|
| A     | 1–126       | 255.0.0.0 /8   | 16,777,214        |
| B     | 128–191     | 255.255.0.0 /16| 65,534            |
| C     | 192–223     | 255.255.255.0 /24 | 254            |
| D     | 224–239     | Multicast      | —                 |
| E     | 240–255     | Reserved       | —                 |

- **Private IP ranges:** `10.x.x.x`, `172.16–31.x.x`, `192.168.x.x`
- **Loopback:** `127.0.0.1`
- **Broadcast:** host bits all 1s (e.g., `192.168.1.255`)

#### IPv6
- 128-bit address, hexadecimal, colon-separated  
- Example: `2001:0db8:85a3::8a2e:0370:7334`  
- **Key features:** larger address space, built-in IPSec, no NAT needed, simplified header

#### Subnetting
```
Network address  = IP AND Subnet mask
Broadcast        = IP OR (NOT Subnet mask)
Number of hosts  = 2^(host bits) - 2
```

### 7.2 ARP (Address Resolution Protocol)
- Maps **IP address → MAC address**  
- Broadcasts ARP request; target replies with its MAC  
- **RARP:** Reverse ARP (MAC → IP); replaced by DHCP

### 7.3 ICMP (Internet Control Message Protocol)
- Used for error reporting and diagnostics  
- `ping` uses ICMP Echo Request/Reply  
- `traceroute` uses ICMP Time Exceeded messages

### 7.4 Routing Algorithms

#### Distance Vector (e.g., RIP)
- Each router maintains distance to every destination  
- Bellman-Ford algorithm  
- **Count-to-infinity** problem → solved by split horizon, poisoned reverse

#### Link State (e.g., OSPF)
- Each router has complete topology map  
- Dijkstra's shortest path algorithm  
- Faster convergence; more bandwidth/memory

| Feature          | Distance Vector (RIP) | Link State (OSPF)    |
|------------------|-----------------------|----------------------|
| Algorithm        | Bellman-Ford          | Dijkstra             |
| Knowledge        | Neighbor's view only  | Complete topology    |
| Convergence      | Slow                  | Fast                 |
| Bandwidth usage  | Low                   | High (LSA flooding)  |
| Scalability      | Small networks        | Large networks       |

#### BGP (Border Gateway Protocol)
- **Path vector** protocol; used between Autonomous Systems (AS) on the internet  
- Policy-based routing

### 7.5 NAT (Network Address Translation)
- Maps private IP addresses to one (or few) public IPs  
- Conserves IPv4 address space  
- Types: Static NAT, Dynamic NAT, PAT (Port Address Translation / NAT overload)

---

## 8. Transport Layer

### 8.1 TCP vs UDP

| Feature           | TCP                          | UDP                          |
|-------------------|------------------------------|------------------------------|
| Connection        | Connection-oriented           | Connectionless               |
| Reliability       | Guaranteed delivery           | No guarantee                 |
| Ordering          | In-order delivery             | No ordering                  |
| Error checking    | Yes (checksum + retransmit)   | Checksum only (no retransmit)|
| Flow control      | Yes (sliding window)          | No                           |
| Congestion control| Yes                          | No                           |
| Speed             | Slower                        | Faster                       |
| Header size       | 20–60 bytes                   | 8 bytes                      |
| Use cases         | HTTP, FTP, SMTP, SSH          | DNS, DHCP, video streaming, VoIP |

### 8.2 TCP Three-way Handshake (Connection Establishment)

```
Client              Server
  |── SYN ──────────►|
  |◄──── SYN-ACK ────|
  |── ACK ──────────►|
         (Connected)
```

### 8.3 TCP Four-way Handshake (Connection Termination)

```
Client              Server
  |── FIN ──────────►|
  |◄──── ACK ────────|
  |◄──── FIN ────────|
  |── ACK ──────────►|
       (TIME_WAIT → CLOSED)
```

### 8.4 TCP Flow Control & Congestion Control

- **Flow Control:** Receiver advertises **window size** (receiver buffer); prevents overflow
- **Congestion Control mechanisms:**
  - **Slow Start:** Window starts at 1 MSS, doubles each RTT until threshold
  - **Congestion Avoidance:** Linear increase after threshold
  - **Fast Retransmit:** Retransmit on 3 duplicate ACKs (without waiting for timeout)
  - **Fast Recovery:** Halve window after fast retransmit (not slow start)

### 8.5 Ports

| Range        | Type                | Examples                          |
|--------------|---------------------|-----------------------------------|
| 0–1023       | Well-known ports    | HTTP(80), HTTPS(443), FTP(21), SSH(22), DNS(53) |
| 1024–49151   | Registered ports    | MySQL(3306), MongoDB(27017)       |
| 49152–65535  | Dynamic/private     | Ephemeral client ports            |

---

## 9. Application Layer Protocols

### 9.1 DNS (Domain Name System)

> Translates domain names (www.google.com) to IP addresses.

```
DNS Resolution Flow:
Browser Cache → OS Cache → Recursive Resolver →
Root Nameserver → TLD Nameserver (.com) →
Authoritative Nameserver → IP Address
```

- **Record types:**

| Type  | Purpose                           |
|-------|-----------------------------------|
| A     | Domain → IPv4 address             |
| AAAA  | Domain → IPv6 address             |
| CNAME | Alias to another domain           |
| MX    | Mail exchange server              |
| NS    | Nameserver for domain             |
| TXT   | Text records (SPF, DKIM)          |
| PTR   | Reverse DNS (IP → domain)         |

### 9.2 HTTP / HTTPS

- **HTTP** (HyperText Transfer Protocol) — Port 80; stateless; application layer protocol
- **HTTPS** — HTTP + TLS/SSL encryption; Port 443

**HTTP Methods:**

| Method  | Description              |
|---------|--------------------------|
| GET     | Retrieve data            |
| POST    | Send data to server      |
| PUT     | Update/replace resource  |
| PATCH   | Partial update           |
| DELETE  | Remove resource          |
| HEAD    | GET without body         |
| OPTIONS | Describe communication options |

**HTTP Status Codes:**

| Range | Category     | Examples                              |
|-------|--------------|---------------------------------------|
| 1xx   | Informational| 100 Continue                          |
| 2xx   | Success      | 200 OK, 201 Created, 204 No Content   |
| 3xx   | Redirection  | 301 Moved Permanently, 302 Found      |
| 4xx   | Client Error | 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found |
| 5xx   | Server Error | 500 Internal Server Error, 503 Service Unavailable |

**HTTP versions:**
- **HTTP/1.1** — persistent connections, pipelining
- **HTTP/2** — multiplexing, binary framing, header compression, server push
- **HTTP/3** — built on QUIC (UDP-based); lower latency

### 9.3 Other Key Protocols

| Protocol | Port | Description                                        |
|----------|------|----------------------------------------------------|
| FTP      | 21(ctrl), 20(data) | File Transfer Protocol         |
| SFTP     | 22   | Secure FTP (over SSH)                              |
| SSH      | 22   | Secure Shell; encrypted remote login               |
| Telnet   | 23   | Unsecured remote login (deprecated)                |
| SMTP     | 25   | Simple Mail Transfer Protocol (sending email)      |
| POP3     | 110  | Post Office Protocol v3 (download email)           |
| IMAP     | 143  | Internet Message Access Protocol (access email)    |
| DHCP     | 67/68| Dynamic Host Configuration Protocol (IP assignment)|
| SNMP     | 161  | Simple Network Management Protocol                 |
| NTP      | 123  | Network Time Protocol                              |
| RDP      | 3389 | Remote Desktop Protocol                            |

---

## 10. Network Security

### 10.1 TLS/SSL
- **TLS (Transport Layer Security)** — cryptographic protocol for securing communications  
- **TLS Handshake:** Client Hello → Server Hello + Certificate → Key Exchange → Finished  
- **Certificates:** issued by Certificate Authorities (CA); X.509 format

### 10.2 Common Attacks

| Attack        | Description                                      | Prevention                    |
|---------------|--------------------------------------------------|-------------------------------|
| DoS/DDoS      | Overwhelm server with requests                   | Rate limiting, CDN, firewall  |
| Man-in-Middle | Intercept communication                          | TLS/HTTPS, certificate pinning|
| Phishing      | Trick users to reveal credentials                | User awareness, SPF/DKIM      |
| SQL Injection | Inject SQL via input fields                      | Prepared statements, WAF      |
| ARP Spoofing  | Fake ARP replies to intercept traffic            | Dynamic ARP inspection        |
| DNS Spoofing  | Corrupt DNS cache with fake records              | DNSSEC                        |
| SYN Flood     | Half-open TCP connections overwhelm server       | SYN cookies                   |

### 10.3 Firewalls and VPN
- **Firewall:** filters traffic based on rules (packet filtering, stateful, application-layer)
- **VPN (Virtual Private Network):** encrypted tunnel over public network; protocols: IPSec, OpenVPN, WireGuard

---

## 11. Wireless Networks

| Standard    | Max Speed | Frequency | Notes               |
|-------------|-----------|-----------|---------------------|
| 802.11a     | 54 Mbps   | 5 GHz     | Less interference   |
| 802.11b     | 11 Mbps   | 2.4 GHz   | Longer range        |
| 802.11g     | 54 Mbps   | 2.4 GHz   | Backward compatible |
| 802.11n (WiFi 4) | 600 Mbps | 2.4/5 GHz | MIMO          |
| 802.11ac (WiFi 5)| 3.5 Gbps | 5 GHz    | Beamforming    |
| 802.11ax (WiFi 6)| 9.6 Gbps | 2.4/5/6 GHz | OFDMA     |

---

## 12. Real-world Use Cases

| Concept        | Real-world Example                                    |
|----------------|-------------------------------------------------------|
| DNS            | Typing `google.com` → IP resolved transparently      |
| CDN            | Netflix/YouTube serving content from edge nodes      |
| Load Balancing | AWS ELB distributing traffic across EC2 instances    |
| VPN            | Remote work accessing corporate intranet securely    |
| BGP            | Internet routing between ISPs and large enterprises  |
| TLS            | Every HTTPS website (bank, e-commerce)               |
| NAT            | Home router sharing one public IP among all devices  |

---

## 13. Frequently Asked Questions (FAQs)

**Q1. What are the differences between TCP and UDP?**  
> TCP is connection-oriented, reliable, and ordered; used for HTTP, FTP. UDP is connectionless, unreliable, and faster; used for DNS, streaming, VoIP.

**Q2. Explain the OSI model layers and their functions.**  
> 7 layers: Physical (bits), Data Link (frames/MAC), Network (packets/IP), Transport (segments/TCP), Session, Presentation (encryption), Application (HTTP/DNS).

**Q3. What happens when you type a URL in a browser?**  
> DNS resolution → TCP 3-way handshake → TLS handshake (for HTTPS) → HTTP GET request → Server response → Browser renders HTML.

**Q4. What is subnetting?**  
> Dividing a network into smaller sub-networks using a subnet mask. Reduces broadcast domains, improves security and performance.

**Q5. What is the difference between a hub, switch, and router?**  
> Hub (L1): broadcasts to all ports. Switch (L2): forwards based on MAC address. Router (L3): routes based on IP address between networks.

**Q6. What is NAT and why is it used?**  
> Network Address Translation maps private IPs to public IPs. Used to conserve IPv4 addresses and provide a layer of security.

**Q7. What is the difference between HTTP and HTTPS?**  
> HTTP transmits data in plain text; HTTPS encrypts data with TLS/SSL. Both use same methods/status codes; HTTPS runs on port 443.

**Q8. What is DNS and how does it work?**  
> DNS translates domain names to IP addresses. A recursive resolver queries root → TLD → authoritative nameserver to get the IP.

**Q9. What is ARP?**  
> Address Resolution Protocol maps an IP address to a MAC address. A host broadcasts "Who has IP X?" and the owner replies with its MAC.

**Q10. Explain the difference between routing and switching.**  
> Switching operates at Layer 2 (MAC addresses, within a network). Routing operates at Layer 3 (IP addresses, between networks).

---

## 14. Common Misconceptions

- ❌ *"MAC addresses are globally unique forever"* → MACs can be spoofed using software.  
- ❌ *"Ping uses TCP"* → ICMP (connectionless); no TCP/UDP involved.  
- ❌ *"UDP is always unreliable"* → UDP is unreliable by protocol, but applications (like QUIC) can add reliability on top.  
- ❌ *"IP addresses are permanent"* → DHCP assigns dynamic IPs; only static IPs are permanent.  
- ❌ *"VPN makes you anonymous"* → VPN hides your IP from websites but VPN provider can see traffic; VPN ≠ full anonymity.

---

## 15. Quick Revision Checklist

- [ ] OSI 7 layers + PDU at each layer  
- [ ] TCP/IP 4-layer model  
- [ ] TCP 3-way handshake  
- [ ] TCP vs UDP comparison  
- [ ] IPv4 address classes + subnetting  
- [ ] DNS resolution steps  
- [ ] HTTP methods + status codes  
- [ ] ARP, ICMP, DHCP roles  
- [ ] Routing algorithms (RIP vs OSPF vs BGP)  
- [ ] Hub vs Switch vs Router  
- [ ] NAT, VPN, TLS/SSL basics  
- [ ] Common port numbers  
- [ ] Error detection methods (parity, CRC)  
- [ ] CSMA/CD vs CSMA/CA  

---

*Last updated: 2026 | Suitable for: GATE, university exams, software engineering interviews*
