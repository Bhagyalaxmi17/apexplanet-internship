Networking Basics:

A. OSI Model: Layers & Functions
The OSI (Open Systems Interconnection) model is a conceptual framework to understand how network communications takes place. It has total 7 layers:

| Layer | Function | Example/Protocol |
|-------|---------|----------------|
| 7 - Application | Provides network services to applications | HTTP, FTP, DNS |
| 6 - Presentation | Data translation, encryption, compression | SSL/TLS, JPEG, ASCII |
| 5 - Session | Manages sessions/connection between apps | NetBIOS, RPC |
| 4 - Transport | Reliable data transfer, flow control, error checking | TCP, UDP |
| 3 - Network | Routing & addressing of data packets | IP, ICMP |
| 2 - Data Link | Error detection, MAC addressing, frames | Ethernet, ARP |
| 1 - Physical | Transmission of raw bits over a medium | Cables, Hubs, Switches |

B. TCP/IP Protocol Suite
The TCP/IP model is the practical model used in real networks. It has total 4 layers:

| Layer | Function | Protocols |
|-------|---------|-----------|
|4- Application | Interfaces for software to use the network | HTTP, HTTPS, FTP, SMTP, DNS |
|3- Transport | End-to-end communication & reliability | TCP, UDP |
|2- Internet | Logical addressing and routing | IP, ICMP, ARP |
|1- Network Access (Link) | Physical network communication | Ethernet, Wi-Fi, ARP |

C. DNS & HTTP/HTTPS Deep Dive
1. DNS - Domain Name System
- Translates human-readable domain names into IP addresses.  
- Example: www.google.com as 142.250.190.78. 
2. HTTP - Hypertext Transfer Protocol 
- Protocol for transferring web pages.  
- Stateless, text-based.  
3. HTTPS - HTTP Secure
- HTTP over TLS/SSL, encrypted communication.  
- Ensures confidentiality, integrity, and authentication.

D. IP Addressing, Subnetting, and NAT
1. IP Addressing
- IPv4: 32-bit address Eg:192.168.1.10
- IPv6: 128-bit address Eg:2001:0db8:85a3::8a2e:0370:7334
2. Subnetting
- Divides a network into smaller sub-networks.  
- Example:  
  - Network:192.168.1.0/24- 256 addresses  
  - Subnet mask:28- 16 addresses per subnet  
3. NAT - Network Address Translation
- Translates private IPs to public IPs for Internet access.  
- Types:  
  a.Static NAT – One-to-one mapping  
  b.Dynamic NAT – Maps multiple private IPs to public pool  
  c.PAT (Port Address Translation) – Multiple private IPs share one public IP using ports  

Conclusion:
- OSI & TCP/IP models help understand network communication.  
- DNS translates domain names; HTTP/HTTPS transfers web content.  
- IP addressing, subnetting, and NAT manage and route data efficiently across networks.
