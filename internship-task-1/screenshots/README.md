Lab Screenshots & Notes
This folder contains screenshots and notes for internship tasks demonstrating basic cybersecurity tools, network setup, and vulnerable VM lab environment.

1. Kali Login Page- (kali-login.png)
- Screenshot: Login screen of Kali Linux VM.
- Notes: Demonstrates accessing the Kali VM environment for cybersecurity labs.

2. Metasploitable2 Login Page- (metasploitable-login.png)
- Screenshot: Login screen of Metasploitable2 VM.
- Notes: Demonstrates accessing the intentionally vulnerable VM used for penetration testing exercises.

3. Kali IFCONFIG Page- (kali-ifconfig.png)
- Screenshot: Terminal showing ifconfig output in Kali VM.
- Notes: Displays Kali’s network interfaces, IP addresses, and host configuration.

4. Metasploitable2 IFCONFIG Page- (metasploitable-ifconfig.png)
- Screenshot: Terminal showing ifconfig output in Metasploitable2 VM.
- Notes: Displays Metasploitable2 network configuration, used to connect and test between VMs.

5. Ping from Kali to Metasploitable2- (kali-ping-metasploitable.png)
- Screenshot: Kali terminal showing ping <Metasploitable2 IP> results.
- Notes: Verifies connectivity between Kali and Metasploitable2 VMs in the host-only network.

6. Wireshark (Packet Capture)- (Wireshark-capture.png)
- Desc- Wireshark is used to capture and analyze network packets between devices.
  It helps in understanding traffic flow and detecting unusual or suspicious activity.
- Screenshot: Full-screen Wireshark capture.
- Notes: Captured network packets (ICMPv6) to observe communication between VMs.

7. Nmap (Network Scanning)- (nmap-scanning.png)
- Desc- Nmap scans devices to identify open ports and running services.
    It helps in mapping the network and discovering potential security vulnerabilities.
- Screenshot: Terminal showing `nmap localhost` scan.
- Notes: Scanned open ports on local machine; optional mention of scanning other VMs or hosts if accessible.

8. Burp Suite (Web Proxy)- (burpsuite-page.png)
- Desc- Burp Suite intercepts and inspects web traffic between browser and server.
    It’s used to analyze and test web application security.
- Screenshot: Main interface showing tabs (Proxy, Target, Repeater, Intruder).
- Notes: Web proxy tool to intercept and analyze HTTP requests; used for web security testing.

9. Netcat (Network Debugging)- (netcat-debugging.png)
- Desc- Netcat creates TCP/UDP connections to test and debug networks.
    It allows simple data transfer, port scanning, and checking connectivity between hosts.
- Screenshot: Server + Client terminals side by side showing live message transfer.
- Notes: Netcat demo: server listens on port 1234, client connects → messages transferred successfully.

10.  5-min Video walkthrough of lab setup
- Notes: Video demonstrates opening all tools and basic usage; shows how to use Wireshark, Nmap, Burp          Suite, and Netcat together between Kali and Metasploitable2 VMs.
