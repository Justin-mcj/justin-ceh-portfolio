# OSI Model

## Overview
Foundational networking topic covering the OSI (Open Systems Interconnection) 7-layer model, how data is encapsulated as it moves down the stack on a sending host, and decapsulated as it moves up the stack on a receiving host.

## Objectives
- Understand the purpose and order of the 7 OSI layers
- Understand encapsulation: how each layer wraps data with its own header
- Understand decapsulation: how each layer inspects and strips its header on the receiving end
- Understand the relationship between the OSI model and the simplified TCP/IP model
- Get comfortable with basic host networking commands (`ipconfig`)

## Concepts Learned

**The 7 layers (top to bottom):**
7. Application
6. Presentation
5. Session
4. Transport
3. Network
2. Data Link
1. Physical

**Encapsulation (sender side):**
- Layer 7 (Application) — application data is generated
- Layer 6 (Presentation) — handles formatting/encryption (e.g. TLS/SSL)
- Layer 5 (Session) — manages the session between two devices, including half-duplex/full-duplex dialogue control
- Layer 4 (Transport) — adds a header containing protocol and port number info → unit becomes a **segment**
- Layer 3 (Network) — adds source/destination IP addresses → unit becomes a **packet**
- Layer 2 (Data Link) — adds source/destination MAC addresses → unit becomes a **frame**
- Layer 1 (Physical) — converts the frame into raw **bits** for transmission (no header added at this layer)

**Decapsulation (receiver side):**
- Layer 1 — bits are received and reassembled into a frame
- Layer 2 — checks destination MAC address; if it matches the local device, strips the Layer 2 header and passes the packet up. If not, the frame is dropped.
- Layer 3 — checks destination IP address; if it matches, strips the header and passes the segment up
- Layer 4 — checks destination port and protocol to determine which application the data belongs to, then strips the header
- Layer 5–7 — the application layer (in the TCP/IP model these are consolidated) handles decoding/decrypting the data back into usable application content

**Layer 2 vs Layer 3 — why MAC vs IP:**
- IP addresses (Layer 3) identify the correct *network/device* to route traffic to, and routers use this to move packets between networks.
- MAC addresses (Layer 2) identify the correct *device on a local network*, and switches use this to deliver frames to the right host once traffic is on that network.
- MAC addresses are intended to be globally unique physical identifiers assigned by the manufacturer, which is why they're suited for local device-level delivery (note: they can be spoofed in software, which is relevant for later security topics).

**Connection to the TCP/IP model:**
Independently reasoned that the OSI model's Layers 5–7 (Session, Presentation, Application) map onto the single "Application" layer of the simplified TCP/IP model, since all three OSI layers are ultimately concerned with preparing/interpreting data for the application.

## Practical Work
Ran `ipconfig` in Windows PowerShell to inspect local network configuration.

**Command used:**
```
ipconfig
```
<img width="629" height="140" alt="image" src="https://github.com/user-attachments/assets/6c93d314-bc31-4732-95c7-41dab12e839a" />

```
ipconfig /all
```
<img width="1186" height="310" alt="image" src="https://github.com/user-attachments/assets/10771de8-137d-4b3d-a319-45e75126b3dd" />

```
getmac
```
<img width="710" height="267" alt="image" src="https://github.com/user-attachments/assets/a7d00fda-48da-4715-bf06-cf4a7f0e9156" />

```
getmac /v
```
<img width="1020" height="240" alt="image" src="https://github.com/user-attachments/assets/a9eeae02-cd28-449a-85a3-5b70a5da9583" />


**Observed output included:**
- Multiple network adapters (several disconnected WiFi adapter entries, two VMware virtual adapters — VMnet1 and VMnet8 — and an active WiFi adapter)
- Active WiFi adapter (`Wireless LAN adapter WiFi`) showing:
  - IPv4 Address: 192.168.185.23
  - Subnet Mask: 255.255.255.0
  - Default Gateway: 192.168.185.144
  

**Observation/troubleshooting:**
Expected to see a MAC address in the output but did not find one. Correctly reasoned that plain `ipconfig` does not display physical (MAC) addresses by default. Confirmed that `ipconfig /all` (showing "Physical Address") or the `getmac` command would be needed to view this.

## Tools Used
- Windows PowerShell
- `ipconfig`
- `ipconfig /all`
- `getmac`
- `getmac /v`

## Results
Successfully retrieved and correctly interpreted local IPv4 configuration details (IP address, subnet mask, default gateway) for the active network adapter, and correctly diagnosed the absence of MAC address information in the default command output.

## Problems Encountered
Initially expected `ipconfig` to display the MAC address alongside the IP configuration details; it did not. Root cause identified as a limitation of the default `ipconfig` command rather than a system issue.

## Key Lessons
- The OSI model is best understood as a strict up/down conversation between matching layers on sender and receiver — each layer only cares about the header it added itself.
- Address/port checks at each decapsulation step act as gatekeeping logic: a mismatch results in the frame/packet being dropped rather than passed further up the stack.
- Not all networking commands surface the same information by default — command flags matter (`ipconfig` vs `ipconfig /all`).

## Next Steps
- Learn the TCP/IP model in more detail and formally map it against the OSI layers
- Try `ipconfig /all` and `getmac` to view MAC address output directly
- Begin using Wireshark to visually inspect encapsulation/decapsulation in captured traffic
