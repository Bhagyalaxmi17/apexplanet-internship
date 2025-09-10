Step1- Reconnaissance:
- 1. Whois:
  - Command: whois example.com
  - Example: whois 8.8.8.8
  - Output: Owner of the IP (Google), Netblock information, Organization, registration dates.
  - Result: Skipped in lab due Host-Only Network Enabled. Normally, this would show registrar, IP, and domain info.
- 2. Nslookup- (nslookup.png)
  - Screenshot: DNS mapping of the target VM(IP mapping of Metasploitable2 VM in host-only network).
- 3. Dig- (dig.png)
  - Screenshot: Detailed DNS information of the target VM(Metasploitable2 VM).
- 4. Google Dorking
  - Example: site:google.com filetype:pdf   AND    site:google.com intitle:"index of"
  - Output: - https://www.google.com/intl/en/about/pdf/Google_Annual_Report_2022.pdf
            - https://www.google.com/intl/en/privacy/pdf/Privacy_Policy.pdf
            - AND Index of /files , Index of /documents , Index of /reports
  - Result: This would show publicly exposed PDF files on the target domain AND this would reveal                        directories and files exposed publicly on the target website.
- 5. Shodan
  - Command: Go to https://www.shodan.io In Search bar Search the domain you want information of.
  - Example: Search 8.8.8.8 on https://www.shodan.io
  - Output: IP: 8.8.8.8, Organization: Google LLC, ISP: Google, Open Ports: 53 (DNS), Services: DNS, Location: United States
  - Result: This shows open ports, services, organization, ISP, and location of the target IP or domain.

- 6. Ping Sweep- (sweep.png)
  - Screenshot: Shows whether target host(Metasploitable2 VM) is alive and responding.
- 7. Banner Grabbing – FTP- (bftp.png)
  - Screenshot: Service type and version of FTP server.FTP banner successfully grabbed from Metasploitable2.
- 8. Banner Grabbing – SSH- (bssh.png)
  - Screenshot: Service type and version of SSH server.SSH banner successfully grabbed from Metasploitable2.

Step2-  Port & Service Scanning:
- 
