
STEP4-  Packet Analysis with Wireshark

1- Capture HTTP, FTP, DNS Traffic –

 - Wireshark is a network protocol analyzer that captures and inspects network packets.
  
 - A. HTTP Traffic: Shows web requests/responses between client and server. Unencrypted HTTP traffic can reveal URLs, headers, and even form data.
    - Commands:
        - curl -I http://192.168.56.102
         - -I asks for the HTTP headers only (sends a HEAD request). You’ll see response headers like HTTP/1.1 200 OK, Content-Type, Server, Date, etc.
        - curl http://192.168.56.101/   # You’ll see the HTML source(Ip of the victime's machine will be used)
        - curl -X POST -d "username=testuser&password=MyP@ss123" http://192.168.56.102/
           - -X POST forces an HTTP POST request.-d "..." sets the POST body — here it simulates a form submission with two fields: username and password.
    - Wireshark Filters:
     - HTTP (all) : http
     - HTTP POST : http.request.method == "POST"
     - HTTP GET : http.request.method == "GET"    

 - B. FTP Traffic: File Transfer Protocol sends files over the network. Classic FTP sends credentials (username/password) in plaintext, making it vulnerable to sniffing.
    - Commands:
       - ftp 192.168.56.101
         - # Name: msfadmin
         - # Password: msfadmin
 - C. DNS Traffic: Domain Name System translates domain names to IP addresses. Capturing DNS traffic lets you see which websites a host is querying. In Wireshark- dns
    - Command:dig @192.168.56.101 example.test A OR 
 - Capturing these allows you to understand what data flows on a network, which is fundamental in cybersecurity monitoring and analysis.

2- Filter Credentials from FTP-

 - FTP sends authentication details in clear text:
   - Username: USER command
   - Password: PASS command
 - By filtering in Wireshark (ftp.request.command == "USER" or "PASS") OR ftp.request.command == "USER" || ftp.request.command == "PASS", you can observe credentials in plaintext.
 - Purpose:
   - Demonstrates why unencrypted protocols are insecure.
   - Highlights importance of using secure alternatives like SFTP or FTPS.

3- Analyze SYN Flood Attack-

 - A SYN flood is a type of Denial-of-Service (DoS) attack that overwhelms a target system by exploiting the - TCP handshake:
 - Attacker sends SYN (connection request) packets rapidly to the server.
 - Server responds with SYN-ACK (acknowledgement).
 - Attacker never sends final ACK, leaving half-open connections.
 - Server resources get exhausted, denying service to legitimate users.
 - Using hping3:
    - Command: sudo hping3 -S --flood -V -p 80 192.168.56.101 (Ip address of victim's system)
    - -S → sets the SYN flag
    - -p 80 → targets port 80 (HTTP)
    - --flood → sends packets as fast as possible
 - Wireshark Filter: tcp.flags.syn==1 && tcp.flags.ack==0
 - Captures incoming SYN packets without corresponding ACKs.
 - Shows the high volume of SYN packets during the attack.
- Purpose:
  - Demonstrates network attacks in a controlled lab.
  - Helps understand how DoS attacks work and the importance of firewall/IDS protections.

STEP 5-   Firewall Basics

1.Create simple iptables rules (allow/deny specific ports).

- Firewall controls network traffic to/from your system.
- Iptables is the default firewall utility in Linux that works at the kernel level.
- In real networks, firewalls control which ports/services are accessible.
- You need to know how to allow/deny traffic using iptables (Linux firewall).
- Rules can:
   - ACCEPT → allow traffic
   - DROP/REJECT → block traffic
   - Rules are applied on different chains:
   - INPUT → incoming traffic
   - OUTPUT → outgoing traffic
   - FORWARD → traffic passing through the machine
- a) Allow a specific port
   - For example, allow SSH (22) only:
   - sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
- b) Deny a specific port
   - For example, block HTTP (80):
   - sudo iptables -A INPUT -p tcp --dport 80 -j DROP
- c) Deny all other incoming traffic
   - sudo iptables -A INPUT -j DROP
- d) List current rules
   - sudo iptables -L -v
- Result:
   - Only SSH connections work; HTTP or other ports are blocked.
   - This is useful for restricting services exposed on your system.
   
2. Demonstrate blocking a port scan attempt

- A port scan is when someone probes your system to see which ports are open.
- Nmap is a common tool to do this.
- Using iptables, you can block traffic to ports you don’t want scanned or limit access to certain IPs.
- To demonstrate that HTTP (port 80) is blocked, you can use Nmap like this:
  - sudo nmap -p 80 192.168.56.101
  - -p 80 → scan only port 80 (the one you blocked)
  - 192.168.56.101 → IP of your Metasploitable VM
- Result: Nmap will show port 80 filtered/closed, demonstrating your firewall rule is working.
- By creating rules, you can control who can access which service, and prevent attackers from easily discovering open ports.
- This is a basic but important part of network hardening in cybersecurity.
