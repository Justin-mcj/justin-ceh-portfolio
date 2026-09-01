# TCP/IP Model

The **TCP/IP model** is a practical networking model that describes how data is communicated across interconnected networks. It is the foundation of Internet communication and is commonly represented using **four layers**.

> **Core idea:** Each layer performs a specific networking function and encapsulates the data received from the layer above it.

## 1. Four-Layer TCP/IP Model

| Layer | Name | Main responsibility | Examples |
|---|---|---|---|
| 4 | Application | Provides network services to applications | HTTP, HTTPS, DNS, SSH, SMTP |
| 3 | Transport | Provides process-to-process communication | TCP, UDP |
| 2 | Internet | Logical addressing and routing | IPv4, IPv6, ICMP |
| 1 | Link | Local-link delivery and transmission | Ethernet, Wi-Fi, ARP |

A common **five-layer teaching model** separates the Link layer into Application, Transport, Network/Internet, Data Link, and Physical. This is useful for learning because it separates local framing from physical transmission.

## 2. TCP/IP and OSI

| TCP/IP | Approximate OSI equivalent |
|---|---|
| Application | Application + Presentation + Session |
| Transport | Transport |
| Internet | Network |
| Link | Data Link + Physical |

The two models are not identical. **TCP/IP describes the protocol architecture used by Internet networking**, while the **OSI model is primarily a reference model**.

### Historical note

TCP/IP development began in the **1970s**. The OSI Basic Reference Model was standardized later and published as **ISO 7498-1 in 1984**.

## 3. Application Layer

The Application layer contains protocols and services used by network applications.

Examples:

- **HTTP** — web communication
- **HTTPS** — HTTP protected by TLS
- **DNS** — domain-name resolution
- **SSH** — secure remote access
- **SMTP** — email transfer

The TCP/IP Application layer roughly covers functions represented by the OSI Application, Presentation, and Session layers.

### Important distinction

Not every Application-layer protocol performs encryption, encoding, and session management itself.

For example:

- HTTP defines web communication.
- TLS provides cryptographic protection for HTTPS.
- Base64 is an encoding scheme and is **not a required part of HTTPS**.

## 4. Transport Layer

The Transport layer provides communication between applications/processes on hosts.

### TCP

TCP is:

- Connection-oriented
- Reliable
- Ordered
- Acknowledgment-based
- Higher-overhead than UDP

TCP uses mechanisms including sequence numbers, acknowledgments, retransmission, and flow control.

### UDP

UDP is:

- Connectionless
- Lightweight
- Lower-overhead
- Without TCP's built-in reliability and ordering mechanisms

UDP is useful when low overhead or timeliness is more important than TCP-style reliability.

### TCP vs UDP

| Feature | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented | Connectionless |
| Reliability | Built-in | No TCP-style reliability |
| Ordering | Built-in | No TCP-style ordering |
| Overhead | Higher | Lower |

> **Modern note:** HTTPS is not inherently TCP-only. Traditional HTTP commonly uses TCP, while **HTTP/3 uses QUIC over UDP**.

## 5. Ports

Port numbers identify the application/process endpoint associated with network traffic.

The range is **0–65,535**.

| Range | Classification |
|---|---|
| 0–1023 | Well-known |
| 1024–49,151 | Registered |
| 49,152–65,535 | Dynamic/private |

### Common ports

| Port | Protocol/service |
|---:|---|
| 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |

A client normally uses an **ephemeral source port**, while a server listens on a known destination port.

Example:

```text
Client: 192.168.1.10:53124
                     └── ephemeral source port

Server: 142.250.x.x:443
                    └── destination/service port
```

## 6. Internet Layer

The Internet layer handles **logical addressing and routing** between networks.

An IP packet contains information such as:

- Source IP address
- Destination IP address
- TTL

Routers primarily operate at this layer because they make forwarding decisions using Layer 3 information.

### IP vs MAC

**IP addresses** provide logical addressing across interconnected networks.

**MAC addresses** provide addressing on the current local link.

A packet can keep the same remote destination IP while its Layer 2 frame is rebuilt from hop to hop.

## 7. Link Layer

The Link layer handles communication across the current local link.

It includes concepts such as:

- MAC addresses
- Ethernet frames
- Wi-Fi frames
- Local delivery
- Switching
- ARP for IPv4 address-to-MAC resolution

### ARP

ARP resolves an IPv4 address to a MAC address on the local network.

Example:

```text
Who has 192.168.1.1?

192.168.1.1
      ↓
BB:BB:BB:BB:BB:BB
```

When sending traffic outside the local network, a host normally needs the MAC address of its **next hop**, commonly the default gateway.

## 8. Encapsulation

When data moves down the TCP/IP stack, each layer encapsulates the information received from the layer above.

```text
Application
    ↓
Application data

Transport
    ↓
[TCP header][Application data]
    = TCP segment

Internet
    ↓
[IP header][TCP segment]
    = IP packet

Link
    ↓
[Frame header][IP packet][Frame trailer]
    = Frame

Physical
    ↓
Bits / signals
```

### Protocol Data Units

| Layer | Common PDU |
|---|---|
| Application | Data |
| Transport | Segment (TCP) / Datagram (UDP) |
| Internet | Packet |
| Link | Frame |
| Physical | Bits/signals |

At the destination, the process is reversed: **Bits → Frame → Packet → Segment → Application data**. This is called **decapsulation**.

## 9. IP and MAC Addresses Across Hops

Consider:

```text
PC → Router A → Router B → Router C → Server
```

The destination IP can remain the remote server's IP as the packet is routed. The Layer 2 frame, however, is rebuilt for each link.

```text
PC → Router A
Destination IP:   Server
Destination MAC:  Router A

Router A → Router B
Destination IP:   Server
Destination MAC:  Router B

Router B → Router C
Destination IP:   Server
Destination MAC:  Router C
```

> **Key principle:** IP addresses identify the logical destination; MAC addresses identify the destination on the current local link.

A PC does not need the MAC address of a remote Internet server to send the packet. It needs the MAC address of its next hop.

## 10. TTL

**TTL (Time To Live)** is a field in an IPv4 packet that limits how long the packet can survive through router hops.

Each forwarding router decreases the TTL.

TTL helps prevent packets from circulating indefinitely because of routing loops.

```text
Initial TTL = 4

Router A → 3
Router B → 2
Router C → 1
Next router → 0 → Packet discarded
```

TTL does **not** mean the number of hops remaining until the destination.

## 11. TCP Three-Way Handshake

TCP establishes a connection using a three-way handshake.

1. **SYN** — Client requests a TCP connection and sends an initial sequence number.
2. **SYN-ACK** — Server acknowledges the client's SYN and sends its own SYN.
3. **ACK** — Client acknowledges the server's response.

```text
Client                    Server
  |                         |
  | -------- SYN ---------> |
  |                         |
  | <----- SYN-ACK -------- |
  |                         |
  | -------- ACK ---------> |
  |                         |
  | === TCP established === |
```

TCP can then exchange application data. The handshake also establishes initial sequence-number information used by TCP's reliability mechanisms.

## 12. Practical HTTPS Flow

A simplified traditional HTTPS-over-TCP flow is:

```text
1. User enters a domain
        ↓
2. DNS lookup
        ↓
3. Server IP obtained
        ↓
4. TCP connection established
        ↓
5. TLS handshake
        ↓
6. HTTP request
        ↓
7. TCP/IP encapsulation
        ↓
8. Network routing
        ↓
9. Server decapsulation
        ↓
10. Server processes request
        ↓
11. HTTP response
        ↓
12. Browser receives and renders resources
```

Modern web communication can differ. For example, **HTTP/3 uses QUIC over UDP**.

## 13. Security Relevance

TCP/IP knowledge is foundational to cybersecurity because attacks, monitoring, and defensive controls often depend on understanding where traffic exists in the stack.

Examples include:

- Packet capture and analysis
- Port scanning
- Firewall rules
- Network segmentation
- ARP spoofing
- IP spoofing
- TCP SYN-based attacks
- DNS attacks
- Intrusion detection
- Traffic analysis

Understanding the model makes tools such as **Wireshark and Nmap** much easier to understand.

## Summary

```text
APPLICATION
Protocols and network services
        ↓
TRANSPORT
TCP / UDP + ports
        ↓
INTERNET
IP addressing + routing
        ↓
LINK
MAC + frames + local delivery
```

The key journey is:

**Application data → Segment → Packet → Frame → Bits**

and the reverse:

**Bits → Frame → Packet → Segment → Application data**
