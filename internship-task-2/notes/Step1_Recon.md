Step 1: Reconnaissance:

Objective -
 - Recon is the first phase of ethical hacking/network security testing.
 - Goal: Gather as much information about the target system as possible without causing disruption.
 - Helps you plan your next steps (scanning and vulnerability assessment) efficiently.
 - There are two main types of recon:
    - Passive Recon: Collect information without directly interacting with the target.
    - Active Recon: Collect information by interacting lightly with the target (ping, banners).
      
Step 1A: Passive Recon
 - Why passive recon first?
 - It’s safe and undetectable.No direct interaction with the target.
 - You can gather data from publicly available sources (like Google or WHOIS).
 - Gives an overview before actually probing the network.
 - These are the tools/methods included in passive recon:
   - 1. Whois – Domain ownership and IP info.
   - 2. Nslookup – DNS records (A, MX, NS).
   - 3. Dig – Detailed DNS info (optional but useful).
   - 4. Google Dorking – Search for publicly exposed sensitive info.
   - 5. Shodan – Discover exposed devices and services online.
 - The tools/methods included in passive recon in detail:
  - 1. Whois
     - Purpose: Find details about the domain/IP ownership.
     - What it tells you: Domain registrar, Creation and expiry dates, Contact info (emails, names), IP addresses allocated to the domain.
     - Why it’s useful:Helps you know who owns the domain, what IP ranges are assigned, and may hint at the organization’s infrastructure.
  - 2. Nslookup
     - Purpose: Check DNS records for the domain.
     - What it tells you: IP addresses (A records), Mail servers (MX records), Name servers (NS records)
     - Why it’s useful: Understanding DNS mapping helps in network mapping and identifying hosts.
  - 3. Dig (optional but recommended)
     - Purpose: Provides detailed DNS info.
     - Why it’s useful: Gives additional info not always visible in Nslookup. Helpful for discovering subdomains or alternate IPs.    
  - 4. Google Dorking
     - Purpose: Search for sensitive info indexed by Google.
     - Why it’s useful: You might find files, directories, or login portals exposed accidentally. Helps understand what the organization leaks publicly.          
  - 5. Shodan
     - Purpose: Scan the internet for devices/services exposed online.
     - What it tells you: Open ports and services, Device types, Vulnerable software versions
     - Why it’s useful: Helps you know if there are live services publicly accessible that could be exploited.

Step 1B: Active Recon
 - Why active recon after passive?
 - Passive gives general info.
 - Active helps validate what’s alive and running on the network.
 - It’s light interaction; doesn’t harm the system.
 - These are the tools/methods included in active recon:
   - 1. Ping Sweep – Identify which hosts in a subnet are alive.
   - 2. Banner Grabbing (FTP) – Check service type and version on FTP port.
   - 3. Banner Grabbing (SSH) – Check service type and version on SSH port.
 - The tools/methods included in active recon in detail:       
  - 1. Ping Sweep
     - Purpose: Identify which hosts in a subnet are alive.Ping command to chcek whether the host is running.
     - Why it’s useful: You don’t waste time scanning dead hosts. Helps you focus on real targets.    
  - 2. Banner Grabbing
     - Purpose:
      -  Check what services and versions are running on open ports.
      -  When you connect to a service (like FTP, SSH, HTTP), many servers announce their software name + version in a banner.
      -  Attackers (or penetration testers) can use that banner info to:
      -  Identify the exact software running.
      -  Look up known vulnerabilities for that version.
      -  Plan targeted attacks or exploits.
      -   Example: vsFTPd 2.3.4 has a backdoor vulnerability (CVE-2011-2523). Just from this banner, we know the system is vulnerable to a specific exploit.
     - Why it’s useful: Knowing service versions helps identify known vulnerabilities.
     - Examples:
       - FTP banner grab → shows version (e.g., 2.3.4)
       - SSH banner grab → shows service details
