1- Capture HTTP, FTP, DNS Traffic –

 - Wireshark is a network protocol analyzer that captures and inspects network packets.
  
 - HTTP Traffic: Shows web requests/responses between client and server. Unencrypted HTTP traffic can reveal URLs, headers, and even form data.
 - FTP Traffic: File Transfer Protocol sends files over the network. Classic FTP sends credentials (username/password) in plaintext, making it vulnerable to sniffing.
 - DNS Traffic: Domain Name System translates domain names to IP addresses. Capturing DNS traffic lets you see which websites a host is querying.
 - Capturing these allows you to understand what data flows on a network, which is fundamental in cybersecurity monitoring and analysis.

2- Filter Credentials from FTP-

 - FTP sends authentication details in clear text:
 - Username: USER command
 - Password: PASS command
 - By filtering in Wireshark (ftp.request.command == "USER" or "PASS"), you can observe credentials in plaintext.
 - Purpose:
 - Demonstrates why unencrypted protocols are insecure.
 - Highlights importance of using secure alternatives like SFTP or FTPS.

3- Analyze SYN Flood Attack-

 - A SYN flood is a type of Denial-of-Service (DoS) attack that overwhelms a target system by exploiting the TCP handshake:
 - Attacker sends SYN (connection request) packets rapidly to the server.
 - Server responds with SYN-ACK (acknowledgement).
 - Attacker never sends final ACK, leaving half-open connections.
 - Server resources get exhausted, denying service to legitimate users.
 - Using hping3:
 - -S → sets the SYN flag
 - -p 80 → targets port 80 (HTTP)
 - --flood → sends packets as fast as possible
 - Wireshark Filter: tcp.flags.syn==1 && tcp.flags.ack==0
 - Captures incoming SYN packets without corresponding ACKs.
 - Shows the high volume of SYN packets during the attack.
Purpose:
Demonstrates network attacks in a controlled lab.
Helps understand how DoS attacks work and the importance of firewall/IDS protections.
