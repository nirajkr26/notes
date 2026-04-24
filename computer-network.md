# Computer Networks (CN) — Super Detailed Notes for Exams & Interviews

---

## 1) Computer Network Basics

A **computer network** connects devices to exchange data and share resources.

### Core metrics
- **Bandwidth:** max theoretical data rate
- **Throughput:** actual delivered rate
- **Latency:** one-way delay
- **Jitter:** variation in delay
- **Loss:** dropped packets percentage

---

## 2) Network Classifications

### By scale
- PAN, LAN, MAN, WAN

### By architecture
- Client-server
- Peer-to-peer

### By topology
- Bus, Star, Ring, Mesh, Tree, Hybrid

---

## 3) OSI Model (7 Layers)

1. Physical
2. Data Link
3. Network
4. Transport
5. Session
6. Presentation
7. Application

### PDUs by layers
- L1: Bits
- L2: Frames
- L3: Packets
- L4: Segments/Datagrams
- L5-L7: Data

### Encapsulation flow
Application data → segment → packet → frame → bits (and reverse on receiver)

---

## 4) TCP/IP Model (Practical)

- Network Access
- Internet
- Transport
- Application

### OSI vs TCP/IP
OSI is conceptual; TCP/IP is deployed internet stack.

---

## 5) Physical Layer Deep Dive

### Transmission media
- Guided: twisted pair, coaxial, fiber
- Unguided: radio, microwave, infrared

### Multiplexing
- FDM
- TDM
- WDM (optical)

### Formulas
- Nyquist (noiseless): `2B log2(V)`
- Shannon (noisy): `B log2(1 + S/N)`

---

## 6) Data Link Layer

### Core functions
- Framing
- MAC addressing
- Error detection/correction
- Flow control
- Medium access control

### Error detection
- Parity
- Checksum
- CRC

### ARQ protocols
- Stop-and-Wait
- Go-Back-N
- Selective Repeat

### MAC protocols
- ALOHA / Slotted ALOHA
- CSMA
- CSMA/CD (Ethernet)
- CSMA/CA (Wi-Fi)
- Token passing

---

## 7) Ethernet and Switching

### Ethernet basics
- IEEE 802.3
- 48-bit MAC address
- Historically CSMA/CD (half duplex shared medium)

### Switch behavior
- Learns MAC-to-port mapping
- For unknown destination: flood
- Supports VLANs and trunking

### VLAN
- Logical segmentation of L2 networks
- Reduces broadcast domain size

---

## 8) Network Layer

### IPv4
- 32-bit address
- CIDR notation (e.g., /24)

### IPv6
- 128-bit address
- Huge address space and simplified header

### Addressing concepts
- Network ID, host ID
- Unicast, multicast, broadcast (IPv4)
- Anycast (IPv6)

### Subnetting (important)
- Hosts in subnet: `2^h - 2`
- Network address: host bits all 0
- Broadcast: host bits all 1

### Protocols
- ARP: IP → MAC
- ICMP: control/diagnostics
- DHCP: dynamic IP assignment
- NAT/PAT: private-to-public translation

---

## 9) Routing

### Static vs dynamic routing
- Static: manual configuration
- Dynamic: auto-learn routes

### Dynamic routing protocols
- RIP (distance vector)
- OSPF (link state)
- BGP (path vector, inter-AS)

### Distance vector vs link state
- Bellman-Ford vs Dijkstra
- Slow vs fast convergence

---

## 10) Transport Layer

### TCP
- Connection-oriented
- Reliable, ordered delivery
- Flow + congestion control

### UDP
- Connectionless
- No reliability guarantee
- Lower overhead, low-latency use cases

### TCP handshake
- SYN → SYN-ACK → ACK

### TCP termination
- FIN/ACK four-step close

### Flow control
- Sliding window based on receiver window

### Congestion control
- Slow start
- Congestion avoidance
- Fast retransmit
- Fast recovery

---

## 11) Application Layer Protocols

### DNS
Hierarchy: resolver → root → TLD → authoritative

Record types: A, AAAA, CNAME, MX, NS, TXT, PTR

### HTTP/HTTPS
- Methods: GET, POST, PUT, PATCH, DELETE, HEAD, OPTIONS
- Status classes: 1xx, 2xx, 3xx, 4xx, 5xx
- HTTPS = HTTP over TLS

### Email protocols
- SMTP (send)
- POP3/IMAP (receive)

### Other common protocols
FTP/SFTP, SSH, Telnet, SNMP, NTP, MQTT

---

## 12) Network Security

### Core controls
- TLS/SSL
- Firewalls (packet/stateful/application)
- IDS/IPS
- VPN (IPSec/OpenVPN/WireGuard)

### Common attacks
- DoS/DDoS
- MITM
- ARP spoofing
- DNS poisoning
- SYN flood
- Session hijacking

### Defenses
- Encryption + certificate validation
- Rate limiting
- Segmentation and zero trust
- MFA
- Secure DNS (DNSSEC/DoH)

---

## 13) Wireless and Mobile Networking

- Wi-Fi generations: 802.11 a/b/g/n/ac/ax
- MIMO, OFDMA, beamforming
- Cellular evolution: 2G → 3G → 4G LTE → 5G

---

## 14) Performance and QoS

### Delay components
- Processing delay
- Queuing delay
- Transmission delay = packet size / link bandwidth
- Propagation delay = distance / propagation speed

### QoS techniques
- Traffic shaping (token bucket, leaky bucket)
- Priority queuing
- DiffServ

---

## 15) Cloud and Modern Networking

- Load balancing (L4/L7)
- CDN for edge caching
- SDN (control plane decoupled)
- NFV (network functions virtualized)
- Service mesh basics in microservices

---

## 16) Most Asked Interview Questions

1. TCP vs UDP practical trade-offs?
2. What happens when you open a website?
3. Why do we need subnetting?
4. Hub vs switch vs router?
5. Explain DNS resolution.
6. How does HTTPS secure communication?
7. What is NAT and why still needed?
8. Difference between RIP, OSPF, BGP?
9. What is congestion control in TCP?
10. Why is HTTP/3 based on QUIC/UDP?

---

## 17) Exam-Focused Topics

- OSI layer responsibilities + examples
- Subnetting numericals
- CRC and flow control concepts
- ARQ protocol comparison
- TCP handshake/termination diagrams
- Routing algorithm differences
- HTTP methods and status codes
- Security attacks and countermeasures

---

## 18) Quick Revision Checklist

- [ ] OSI and TCP/IP mapping
- [ ] Encapsulation/decapsulation steps
- [ ] IPv4/IPv6 and subnetting formulas
- [ ] ARP, ICMP, DHCP, NAT
- [ ] Routing protocols (RIP/OSPF/BGP)
- [ ] TCP vs UDP
- [ ] TCP flow and congestion control
- [ ] DNS + HTTP/HTTPS internals
- [ ] Firewall/VPN/TLS basics
- [ ] Common ports and protocols

---

*Last updated: 2026 | Target: CN exams, GATE prep, and interview preparation*
