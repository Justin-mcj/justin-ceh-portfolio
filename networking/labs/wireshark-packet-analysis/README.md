# Lab 1 — Network Traffic & Packet Analysis with Wireshark

## Objective

Analyze real network traffic using Wireshark and identify how common networking protocols operate across different layers.

## Environment

- OS: Kali Linux
- Tool: Wireshark
- Interface: `wlan0`

## 1. Ethernet and IPv4

The capture showed Ethernet frames carrying IPv4 packets.

I observed:

- Source and destination MAC addresses
- Source and destination IPv4 addresses
- IPv4 TTL values
- Protocol information

This demonstrated the difference between Layer 2 addressing (MAC) and Layer 3 addressing (IP).

## 2. ARP

I captured an ARP request and reply.

The request used the broadcast MAC address:

`ff:ff:ff:ff:ff:ff`

The ARP exchange allowed the device to determine the MAC address associated with an IPv4 address on the local network.

## 3. TCP Three-Way Handshake

I captured a complete TCP connection establishment:

1. SYN
2. SYN-ACK
3. ACK

The capture showed the client initiating a connection to port `443`, followed by the server response and the final acknowledgement from the client.

### TCP Sequence and Acknowledgement Numbers

TCP uses sequence numbers to track bytes of data.

For example, when a segment begins at sequence number `10` and contains `100` bytes, the next expected acknowledgement is:

`110`

Each direction of a TCP connection maintains its own sequence-number space.

## 4. Ports

The capture included:

- Source port: `52302`
- Destination port: `443`

`52302` is an ephemeral client-side port, while `443` is conventionally associated with HTTPS traffic.

## 5. TLS Client Hello

I captured a TLS Client Hello following the TCP connection establishment.

The Client Hello contained information including:

- Random value
- Session information
- Supported cipher suites
- Extensions
- Key Share
- Server Name Indication (SNI)

The SNI observed in the capture was:

`chatgpt.com`

The capture was identified by Wireshark as TLS 1.3 traffic.

The Key Share contains public key-exchange information. It does not expose the client's private key or the resulting session secrets.

## 6. TCP Reassembly

Wireshark showed that the TLS Client Hello was carried across multiple TCP segments and reassembled for protocol analysis.

This demonstrates that application-layer data does not necessarily correspond to a single TCP packet.

## 7. TTL Observation

The capture contained packets with different TTL values.

TTL is reduced by routers as packets travel through networks. The initial TTL cannot always be determined from a captured packet unless the sender's operating system and initial TTL are known.

## Security Relevance

Packet analysis provides visibility into:

- Network communication
- Protocol behaviour
- Connection establishment
- Local network address resolution
- Encrypted-session establishment
- Traffic metadata

Understanding normal network behaviour provides a foundation for identifying unusual or suspicious traffic during security investigations.

## Evidence

Screenshots from the practical Wireshark work are stored in the `evidence/` directory.

## Key Takeaways

- MAC addresses operate at the local-link level.
- IP addresses provide logical network addressing.
- ARP resolves local IPv4 addresses to MAC addresses.
- TCP establishes connections using SYN, SYN-ACK and ACK.
- TCP sequence and acknowledgement numbers track data.
- Port numbers identify communication endpoints and services.
- TLS establishes encrypted communication after the TCP connection.
- TLS handshake metadata can be visible even though application data is encrypted.
- Wireshark can reassemble data carried across multiple TCP segments.