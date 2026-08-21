# Computer Networks — Detailed Interview & Exam Notes

> **Focus:** network fundamentals, OSI/TCP-IP, Ethernet, IP addressing/subnetting, ARP, routing, TCP/UDP, DNS, HTTP/HTTPS, TLS, Wi-Fi, NAT, congestion, troubleshooting, security, numericals, and interview preparation.

## 1. Network Fundamentals

A **computer network** connects hosts so they can exchange data and share resources.

Key metrics:

- **Bandwidth:** maximum theoretical capacity of a link.
- **Throughput:** achieved data rate.
- **Goodput:** useful application data rate excluding protocol overhead/retransmissions.
- **Latency:** time to deliver data.
- **Jitter:** variation in packet delay.
- **Packet loss:** packets that fail to reach the destination.

### Delay components

```text
Total delay ≈ processing + queuing + transmission + propagation
```

- Processing: inspect packet/header.
- Queuing: wait in device queue.
- Transmission: time to push bits onto link = packet bits / link rate.
- Propagation: signal travel time through medium.

## 2. Network Types and Topologies

### Scale

- PAN: personal area.
- LAN: local area.
- MAN: metropolitan area.
- WAN: wide area.

### Topologies

- **Star:** endpoints connect to central switch/AP.
- **Bus:** shared backbone.
- **Ring:** logical/physical ring.
- **Mesh:** many redundant links.

Full mesh links for `n` nodes:

```text
n(n-1)/2
```

## 3. OSI Model

```text
7 Application   HTTP, DNS, SMTP
6 Presentation  encoding, compression, encryption concepts
5 Session       session coordination concepts
4 Transport     TCP, UDP
3 Network       IP, ICMP, routing
2 Data Link     Ethernet, Wi-Fi framing, MAC
1 Physical      bits, signaling, media
```

PDU names commonly used:

```text
Application/Presentation/Session → Data
Transport → Segment (TCP) / Datagram (UDP)
Network → Packet
Data Link → Frame
Physical → Bits
```

### Encapsulation

```text
Application data
  ↓ TCP header
TCP segment
  ↓ IP header
IP packet
  ↓ Ethernet header/trailer
Ethernet frame
  ↓
Bits
```

At the receiver, the reverse process is decapsulation.

## 4. TCP/IP Model

Practical internet model:

1. Application
2. Transport
3. Internet
4. Link/Network Access

OSI is primarily a conceptual teaching model; TCP/IP describes the protocol suite used in real networks.

## 5. Physical Layer

### Media

- Twisted-pair copper.
- Coaxial.
- Fiber optic.
- Wireless radio.

Fiber offers high bandwidth, long distance and strong immunity to electromagnetic interference.

### Nyquist theorem

For an ideal noiseless channel:

```text
Maximum bit rate = 2B log₂(V)
```

where B is bandwidth and V is number of signal levels.

### Shannon capacity

For a noisy channel:

```text
C = B log₂(1 + S/N)
```

where S/N is signal-to-noise ratio.

## 6. Data Link Layer

Responsibilities include:

- Framing.
- MAC addressing.
- Error detection.
- Medium access.
- Link-level flow/control behavior depending on protocol.

### Ethernet

IEEE 802.3 family. Ethernet frames contain source/destination MAC addresses and an EtherType/length field plus payload and frame check sequence.

### MAC address

Typically 48 bits for Ethernet:

```text
AA:BB:CC:DD:EE:FF
```

MAC addresses are link-local identifiers, not globally routable internet addresses.

## 7. Error Detection

### Parity

Adds parity information; limited detection capability.

### Checksum

Combines data words into a checksum; used in several protocols.

### CRC

Cyclic Redundancy Check uses polynomial arithmetic and is strong for detecting burst errors. Ethernet uses an FCS based on CRC.

## 8. Flow Control and Reliable Link Protocols

### Stop-and-Wait

Send one frame and wait for acknowledgement.

Pros: simple. Cons: poor link utilization on high-latency paths.

### Sliding window

Allows multiple outstanding frames.

### Go-Back-N

On loss/error, retransmit the affected frame and subsequent outstanding frames.

### Selective Repeat

Retransmit only missing/incorrect frames. More receiver buffering and protocol complexity.

Do not blindly apply textbook window-size formulas across every variant; sequence-number/window constraints depend on protocol definitions.

## 9. Switching

A **switch** forwards frames using MAC addresses.

### MAC learning

```text
Frame arrives on port 3 from MAC A
→ switch records A → port 3
```

If destination is known, it forwards to the corresponding port. If unknown, it may flood within the VLAN.

### Collision domains

Modern switched Ethernet provides a separate collision domain per switch port in typical full-duplex operation.

### Broadcast domains

Routers/L3 boundaries separate broadcast domains. VLANs also create logical Layer-2 broadcast domains.

## 10. Hub vs Switch vs Router

| Device | Main layer | Decision |
|---|---|---|
| Hub | Physical | Repeats bits/signals |
| Switch | Data link | MAC address |
| Router | Network | IP/prefix/routing table |
| Gateway | Depends | Protocol/application translation concept |

## 11. VLANs

A **VLAN** logically separates Layer-2 networks on shared switching infrastructure.

Common concepts:

- Access port: usually carries one VLAN untagged.
- Trunk: carries multiple VLANs using tagging such as IEEE 802.1Q.
- Inter-VLAN routing: requires a Layer-3 device.

VLANs improve segmentation but are not a substitute for complete security controls.

## 12. IP Addressing

### IPv4

32-bit address:

```text
192.168.1.10
```

IPv4 is usually discussed with CIDR prefixes rather than old classful addressing.

### CIDR

```text
192.168.1.0/24
```

`/24` means 24 network bits and 8 host bits.

### Private ranges

```text
10.0.0.0/8
172.16.0.0/12
192.168.0.0/16
```

### Special addresses

- `127.0.0.0/8`: loopback.
- `0.0.0.0`: unspecified/default-route context depending on usage.
- Link-local IPv4: `169.254.0.0/16`.

## 13. Subnetting

For a prefix `/p` in IPv4:

```text
Host bits = 32 − p
Addresses per subnet = 2^(host bits)
```

Traditional host-count formula:

```text
usable hosts = 2^h − 2
```

This “minus two” rule has exceptions for special prefix sizes and modern point-to-point/networking practices, so know the context.

### Example

`192.168.10.0/26`:

```text
Host bits = 6
Addresses = 2^6 = 64
Traditional usable hosts = 62
```

Subnets inside a /24 occur every 64 addresses:

```text
192.168.10.0/26
192.168.10.64/26
192.168.10.128/26
192.168.10.192/26
```

## 14. IPv6

IPv6 uses 128-bit addresses.

```text
2001:db8:1234::1
```

Features:

- Huge address space.
- Hierarchical addressing.
- No IPv4-style broadcast; multicast/anycast are used instead.
- Neighbor Discovery replaces ARP functionality.
- Simplified base header.

Do not claim that IPv6 “automatically means no NAT” in every deployment; NAT-like mechanisms can exist, although end-to-end addressing is more feasible.

## 15. ARP and Neighbor Discovery

### ARP

Maps IPv4 address to MAC address on a local network.

```text
Host A: Who has 192.168.1.5?
Broadcast ARP request

Host B: 192.168.1.5 is at AA:BB:...
ARP reply
```

ARP cache stores recently learned mappings.

### IPv6

IPv6 uses **Neighbor Discovery Protocol (NDP)** through ICMPv6 rather than ARP.

## 16. Routing

A router forwards packets between networks based on destination prefixes and routing information.

### Longest Prefix Match

If multiple routes match an IP, the most specific (longest prefix) normally wins.

Example:

```text
10.0.0.0/8
10.1.0.0/16
10.1.2.0/24  ← 10.1.2.5 chooses this
```

### Default route

```text
0.0.0.0/0
```

Matches any IPv4 destination when no more-specific route exists.

## 17. Routing Protocols

### RIP

Distance-vector protocol using hop count; limited scalability.

### OSPF

Link-state IGP. Routers build topology information and calculate shortest paths, commonly using Dijkstra/SPF.

### BGP

Path-vector protocol used between autonomous systems. Routing decisions are strongly policy-driven, not simply “shortest path.”

### Static vs dynamic routing

- Static: configured manually; predictable but less adaptive.
- Dynamic: routers exchange routing information and adapt to topology changes.

## 18. NAT

Network Address Translation maps addresses/ports between address spaces.

Common forms:

- Static NAT.
- Dynamic NAT.
- PAT/NAPT: many private endpoints share a public IP using port mappings.

NAT conserves IPv4 addresses and provides some topology hiding, but it is not a complete security boundary by itself.

## 19. ICMP

ICMP carries network control/diagnostic messages.

Examples:

- Echo request/reply → `ping`.
- Time Exceeded → important for traceroute behavior.
- Destination Unreachable.

ICMP is not an application data protocol like HTTP.

## 20. Transport Layer

Transport protocols provide process-to-process communication.

A socket endpoint is commonly identified by IP address + port + protocol context.

### Ports

- 0–1023: well-known.
- 1024–49151: registered.
- 49152–65535: dynamic/private range commonly used for ephemeral ports.

## 21. TCP

TCP provides a reliable, ordered byte stream with congestion and flow control.

Important mechanisms:

- Sequence numbers.
- Acknowledgements.
- Retransmission.
- Receive window.
- Congestion window.
- Connection state machine.

TCP does **not** preserve application message boundaries; it is a byte stream.

## 22. TCP Three-Way Handshake

```text
Client                 Server
  | ---- SYN ---------> |
  | <--- SYN + ACK ---- |
  | ---- ACK ---------> |
```

Purpose:

- Establish connection state.
- Exchange initial sequence numbers.
- Confirm both directions are reachable.

## 23. TCP Connection Termination

Typical graceful close is a four-segment exchange because each direction of the full-duplex byte stream can close independently.

```text
A → FIN → B
A ← ACK ← B
A ← FIN ← B
A → ACK → B
```

`TIME_WAIT` allows old delayed segments to expire and helps ensure the final acknowledgement can be retransmitted if necessary.

## 24. TCP Flow Control vs Congestion Control

### Flow control

Protects the **receiver** from being overwhelmed. The receive window communicates how much data the receiver can accept.

### Congestion control

Protects the **network** from overload. TCP maintains a congestion window and adapts sending behavior to observed loss/ECN/delay signals depending on implementation.

These are different concepts.

## 25. TCP Congestion Control

Classical concepts:

- **Slow start:** congestion window grows rapidly at first.
- **Congestion avoidance:** growth becomes more conservative.
- **Fast retransmit:** duplicate acknowledgements can indicate loss before timeout.
- **Fast recovery:** adjusts sending after inferred loss.

Modern TCP implementations use different congestion-control algorithms; do not assume every TCP stack follows one exact textbook curve.

## 26. UDP

UDP is connectionless and provides datagrams without TCP-style reliability, ordering, retransmission or congestion control.

Advantages:

- Low protocol overhead.
- Application controls reliability/timing if needed.
- Useful for real-time and request/response workloads.

Examples:

- DNS.
- DHCP.
- RTP/media systems.
- QUIC transport uses UDP underneath.

UDP does have a checksum mechanism; “UDP is unreliable” means delivery/ordering is not guaranteed by UDP itself.

## 27. TCP vs UDP

| TCP | UDP |
|---|---|
| Connection-oriented | Connectionless |
| Reliable ordered byte stream | Unreliable datagrams |
| Retransmission | No built-in retransmission |
| Flow control | Yes |
| Congestion control | TCP-specific mechanisms |
| Message boundaries | Not preserved | Datagram boundaries preserved |
| Typical use | Web streams, SSH, DB connections | DNS, media, QUIC transport |

## 28. DNS

DNS maps names to resource records.

Typical resolution:

```text
Application/OS cache
        ↓
Recursive resolver
        ↓
Root
        ↓
TLD server
        ↓
Authoritative server
        ↓
Answer
```

### Important records

| Type | Meaning |
|---|---|
| A | IPv4 address |
| AAAA | IPv6 address |
| CNAME | Alias |
| MX | Mail exchange |
| NS | Authoritative name server |
| TXT | Arbitrary text/policy data |
| PTR | Reverse DNS |
| SOA | Zone authority metadata |

### DNS caching

Resolvers cache records according to TTL, reducing latency and authoritative-server load.

## 29. DHCP

DHCP dynamically configures hosts.

Classic DORA sequence:

```text
Discover → Offer → Request → ACK
```

A DHCP configuration can include IP address, subnet mask/prefix, default gateway and DNS servers.

## 30. HTTP

HTTP is an application-layer request/response protocol.

### Methods

- GET: retrieve.
- POST: submit/create/action.
- PUT: replace/update a resource representation.
- PATCH: partial update.
- DELETE: remove.
- HEAD: headers equivalent to GET without response body.
- OPTIONS: discover supported communication/options.

### Safe vs idempotent

- Safe methods should not intentionally change server state (GET/HEAD/OPTIONS conceptually).
- Idempotent means repeating the same request has the same intended effect as doing it once. PUT and DELETE are generally idempotent by HTTP semantics; POST is generally not.

## 31. HTTP Status Codes

| Range | Meaning |
|---|---|
| 1xx | Informational |
| 2xx | Success |
| 3xx | Redirection |
| 4xx | Client-side request problem |
| 5xx | Server-side failure |

Common:

- 200 OK
- 201 Created
- 204 No Content
- 301/308 permanent redirect
- 302/307 temporary redirect
- 400 Bad Request
- 401 Unauthorized/authentication required
- 403 Forbidden
- 404 Not Found
- 409 Conflict
- 429 Too Many Requests
- 500 Internal Server Error
- 502 Bad Gateway
- 503 Service Unavailable
- 504 Gateway Timeout

## 32. HTTP Headers and Cookies

Common headers:

- `Content-Type`
- `Content-Length`
- `Accept`
- `Authorization`
- `Cache-Control`
- `ETag`
- `If-None-Match`
- `Cookie`
- `Set-Cookie`

### Cookies

Security-relevant attributes:

- `Secure`: send over HTTPS.
- `HttpOnly`: JavaScript cannot read it through `document.cookie`.
- `SameSite`: controls cross-site cookie sending behavior.
- `Domain` / `Path`: scope the cookie.

## 33. HTTP Caching

### Cache-Control

Examples:

```text
Cache-Control: max-age=3600
Cache-Control: no-store
Cache-Control: no-cache
```

`no-cache` means a stored response generally must be revalidated before reuse; it does **not** literally mean “do not store.” `no-store` means do not store.

### ETag

Server returns an entity tag. Client can send:

```text
If-None-Match: "abc123"
```

If unchanged, server can return `304 Not Modified` without sending the full representation.

## 34. HTTP/1.1 vs HTTP/2 vs HTTP/3

### HTTP/1.1

- Textual headers/message format.
- Persistent connections.
- Multiple requests can be serialized on a connection, leading to application-level head-of-line issues.

### HTTP/2

- Binary framing.
- Multiplexed streams over one TCP connection.
- Header compression (HPACK).
- Stream prioritization concepts.

### HTTP/3

- HTTP semantics over **QUIC**.
- QUIC uses UDP as its packet transport substrate.
- QUIC provides encrypted transport, streams and connection migration features.
- Avoid saying HTTP/3 is “HTTP over raw UDP”; QUIC supplies the transport functionality.

## 35. TLS and HTTPS

HTTPS = HTTP carried over TLS.

TLS provides:

- Server authentication through certificates.
- Confidentiality.
- Integrity.

Simplified handshake idea:

```text
ClientHello
   ↓
ServerHello + certificate + key exchange messages
   ↓
Key establishment
   ↓
Encrypted application traffic
```

Modern TLS commonly uses ephemeral key exchange and authenticated symmetric encryption for application data.

## 36. Certificates and PKI

A certificate binds an identity (such as a hostname) to a public key and is signed by a trusted certificate authority chain.

Browser trust involves:

- Certificate validity period.
- Hostname verification.
- Signature chain.
- Key usage/extensions.
- Trust store.
- Revocation/status mechanisms as applicable.

## 37. Sockets

A socket is an OS/network abstraction for communication.

Typical TCP server:

```text
socket()
  ↓
bind()
  ↓
listen()
  ↓
accept()
  ↓
read/write
```

Typical client:

```text
socket()
  ↓
connect()
  ↓
read/write
```

## 38. Load Balancing

A load balancer distributes requests across backend instances.

Common strategies:

- Round robin.
- Weighted round robin.
- Least connections.
- Hash-based routing.

Layer-4 load balancing operates primarily on transport information. Layer-7 load balancing understands application protocols such as HTTP and can route by host/path/header.

## 39. Reverse Proxy

A reverse proxy sits in front of backend servers.

Responsibilities can include:

- TLS termination.
- Routing.
- Compression.
- Caching.
- Rate limiting.
- Authentication integration.
- Load balancing.

## 40. Wi-Fi Basics

Wi-Fi uses IEEE 802.11.

Unlike traditional half-duplex shared Ethernet, Wi-Fi uses **CSMA/CA** because a wireless station cannot reliably detect collisions while transmitting in the same way wired Ethernet historically did.

Concepts:

- Access point.
- SSID.
- Channels.
- Association/authentication.
- WPA2/WPA3 security.
- 2.4/5/6 GHz bands depending on hardware/regulation.

## 41. CSMA/CD vs CSMA/CA

### CSMA/CD

Carrier Sense Multiple Access with Collision Detection. Historically associated with shared half-duplex Ethernet.

### CSMA/CA

Collision Avoidance. Used by Wi-Fi, where stations attempt to reduce collision probability using contention/backoff and acknowledgements.

Modern full-duplex switched Ethernet does not use CSMA/CD for normal operation.

## 42. Network Security

### Common attacks

- Packet sniffing.
- Spoofing.
- ARP poisoning.
- DNS poisoning/cache attacks.
- SYN flood.
- DDoS.
- Man-in-the-middle.
- Session/token theft.

### Defenses

- TLS.
- Secure Wi-Fi configuration.
- Network segmentation/VLANs plus firewall policy.
- Authentication and authorization.
- Rate limiting.
- DDoS protection.
- Secure DNS practices.
- Monitoring and anomaly detection.

## 43. Firewall

A firewall filters traffic according to rules.

- Packet filtering.
- Stateful firewall.
- Application/proxy firewall.
- Network security groups/cloud firewalls.

A firewall is a control layer, not a substitute for secure application code.

## 44. Troubleshooting Method

When “the API is slow/unreachable,” isolate layers:

```text
DNS → TCP/QUIC → TLS → HTTP → application → database
```

Useful tools:

- `ping`
- `traceroute` / `tracert`
- `nslookup` / `dig`
- `curl -v`
- `ss` / `netstat`
- `tcpdump`
- Wireshark

Ask:

1. Does DNS resolve?
2. Is the route reachable?
3. Is the port accepting connections?
4. Does TLS succeed?
5. What HTTP status/latency occurs?
6. Is backend/database latency the bottleneck?

## 45. Common Network Numericals

### Transmission delay

```text
Transmission delay = packet size (bits) / link rate (bits/sec)
```

Example: 1,000-byte packet on 10 Mbps:

```text
8000 / 10,000,000 = 0.0008 sec = 0.8 ms
```

### Propagation delay

```text
Propagation delay = distance / propagation speed
```

### Bandwidth-delay product

```text
BDP = bandwidth × RTT
```

It approximates how much data can be “in flight” on a path.

### Subnetting

For `/27`:

```text
Host bits = 5
Addresses = 32
Traditional usable hosts = 30
```

## 46. Interview Questions & Answers

### Q1. Bandwidth vs throughput?

Bandwidth is link capacity; throughput is actual achieved rate.

### Q2. Latency vs throughput?

Latency is delay per operation/path; throughput is amount of data completed per unit time. A system can have high throughput and high latency.

### Q3. Why seven OSI layers?

They provide a conceptual separation of responsibilities. Real protocols do not always map perfectly one-to-one to OSI layers.

### Q4. Hub vs switch?

A hub repeats signals to ports; a switch learns MAC addresses and forwards frames selectively.

### Q5. Switch vs router?

A switch primarily forwards frames within Layer-2 networks; a router forwards IP packets between networks.

### Q6. What is ARP?

IPv4-to-MAC resolution on a local Layer-2 network.

### Q7. Does a router forward MAC addresses end-to-end?

No. Ethernet MAC addresses are link-local. At each routed hop, the Layer-2 frame is rebuilt for the next link.

### Q8. What is CIDR?

Classless Inter-Domain Routing represents networks using prefix length such as `/24`, enabling flexible address allocation and routing aggregation.

### Q9. What is longest prefix match?

When multiple routing entries match a destination, the most specific matching prefix is selected.

### Q10. TCP vs UDP?

TCP provides an ordered reliable byte stream with flow/congestion control. UDP provides datagrams without TCP-style reliability/ordering.

### Q11. Why is TCP called a byte stream?

TCP does not preserve application message boundaries; the receiver reads a sequence of bytes.

### Q12. Why three-way handshake?

It establishes bidirectional connection state and synchronizes sequence-number state before normal data exchange.

### Q13. Why TIME_WAIT?

It helps prevent delayed old segments from interfering with a future connection and permits retransmission of the final ACK when needed.

### Q14. Flow control vs congestion control?

Flow control protects the receiver; congestion control protects the network.

### Q15. What is DNS?

A distributed naming system that maps domain names to records such as IP addresses and mail servers.

### Q16. What happens when you type a URL?

A simplified path is: parse URL → DNS resolution → establish TCP/QUIC → TLS for HTTPS → send HTTP request → server/proxy/backend work → response → browser processing/rendering. Caches and connection reuse can skip parts.

### Q17. What is HTTPS?

HTTP transported over TLS, providing encryption, integrity and server authentication.

### Q18. HTTP/2 vs HTTP/1.1?

HTTP/2 uses binary framing and multiplexed streams over a connection, reducing many HTTP/1.1 connection-level inefficiencies.

### Q19. HTTP/3 vs HTTP/2?

HTTP/3 uses QUIC instead of TCP, providing stream multiplexing without TCP-level head-of-line blocking across independent streams and supporting modern transport features.

### Q20. What is NAT?

A mechanism translating addresses and often ports between network domains, commonly used to let private IPv4 hosts share public addresses.

### Q21. Is NAT a firewall?

No. NAT changes addressing; firewalling is an explicit traffic-policy/security function.

### Q22. What is a VLAN?

A logical Layer-2 segmentation mechanism that creates separate broadcast domains over switching infrastructure.

### Q23. What is a reverse proxy?

A server in front of backend servers that can terminate TLS, route requests, cache, rate-limit and load-balance.

### Q24. What is a CDN?

A geographically distributed caching/delivery system that serves content closer to users, reducing latency and origin load.

### Q25. How do you debug “website not opening”?

Check DNS, route/connectivity, destination port, TLS handshake, HTTP status and backend dependencies in order. Use `dig`, `curl`, `ss`, `traceroute` and packet capture as appropriate.

### Q26. Why can ping work while HTTP fails?

ICMP reachability does not imply that TCP port 443/80 is reachable, TLS succeeds, or the HTTP application is healthy. Firewalls may treat protocols differently.

### Q27. Why can DNS be fast but the request still slow?

DNS is only one stage. TCP/TLS setup, network latency, queueing, server processing, database calls and response transfer can dominate.

### Q28. What is BGP?

The internet's inter-domain path-vector routing protocol. It exchanges reachability and applies policy between autonomous systems.

### Q29. What is a subnet mask?

It identifies which bits of an IPv4 address represent the network prefix versus host portion.

### Q30. What is BDP?

Bandwidth-delay product: bandwidth multiplied by round-trip or path delay, approximating the amount of data that can be in flight.

## 47. Revision Checklist

- [ ] Bandwidth/latency/throughput/goodput
- [ ] Delay components
- [ ] OSI/TCP-IP models
- [ ] Encapsulation/decapsulation
- [ ] Ethernet/MAC/switching
- [ ] VLAN/broadcast/collision domains
- [ ] IPv4/CIDR/private ranges
- [ ] Subnetting numericals
- [ ] IPv6/NDP
- [ ] ARP/ICMP
- [ ] Routing/longest prefix match
- [ ] RIP/OSPF/BGP
- [ ] NAT/PAT
- [ ] TCP/UDP
- [ ] TCP handshake/termination/TIME_WAIT
- [ ] Flow vs congestion control
- [ ] DNS/DHCP
- [ ] HTTP methods/status/cache
- [ ] TLS/HTTPS/PKI
- [ ] HTTP/1.1 vs HTTP/2 vs HTTP/3
- [ ] Sockets/load balancers/reverse proxies
- [ ] Wi-Fi/CSMA/CA
- [ ] Firewalls/network security
- [ ] Troubleshooting tools
- [ ] Transmission/propagation/BDP numericals
