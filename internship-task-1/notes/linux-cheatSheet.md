Linux Cheat Sheet:

1. File System Navigation
| Command | Description | Example |
|---------|------------|---------|
| pwd | Print current working directory | $ pwd → /home/user |
| ls | List files and directories | $ ls → file1.txt file2.txt |
| ls -l | List with details (permissions, owner, size, date) | $ ls -l |
| ls -a | List all files, including hidden | $ ls -a |
| cd <directory> | Change directory | $ cd /home/user/Documents |
| cd .. | Move one directory up | $ cd .. |
| cd ~ | Go to home directory | $ cd ~ |

2. File & Directory Permissions
| Command | Description | Example |
|---------|------------|---------|
| chmod | Change file/directory permissions | $ chmod 755 file.txt |
| chown | Change file/directory owner | $ chown user:group file.txt |
| ls -l | View permissions | -rwxr-xr-- 1 user group file.txt |
| chmod -R <mode> <directory> | Change permissions recursively | $ chmod -R 755 /var/www/html |
| chown -R <user>:<group> <directory> | Change owner recursively | $ chown -R user:group /var/www/html |
Permission Notation:
- r- read, w- write, x- execute  
- Example: 755- wxr-xr-x  

3. Package Management
| Command | Description | Example |
|---------|------------|---------|
| apt update | Update package list | $ sudo apt update |
| apt upgrade | Upgrade installed packages | $ sudo apt upgrade |
| apt install <package> | Install a package | $ sudo apt install vim |
| apt remove <package> | Remove a package | $ sudo apt remove vim |
| dpkg -i <package.deb> | Install a .deb package manually | $ sudo dpkg -i package.deb |
| dpkg -l | List installed packages | $ dpkg -l |

4. Networking Commands
| Command | Description | Example |
|---------|------------|---------|
| ifconfig | Show network interfaces & IP addresses | $ ifconfig |
| ping <host> | Check connectivity to a host | $ ping google.com |
| netstat -tulnp | Show active connections & listening ports | $ netstat -tulnp |
| traceroute <host> | Trace route packets take to reach host | $ traceroute google.com |
| curl <url> | Fetch content from URL | $ curl https://example.com |
| wget <url> | Download files from URL | $ wget https://example.com/file.zip |
| nmap <host> | Network scanning & host discovery | $ nmap 192.168.1.10 |
| nmap -sV <host> | Scan open ports & service versions | $ nmap -sV 192.168.1.10 |
| tcpdump -i <interface> | Capture network packets | $ sudo tcpdump -i eth0 |

Additional: Process & System Management
| Command | Description | Example |
|---------|------------|---------|
| ps aux | Show all running processes | $ ps aux |
| top | Interactive view of running processes & system resources | $ top |
| htop | Improved interactive process viewer (needs install) | $ htop |
| kill <PID> | Kill a process by Process ID | $ kill 1234 |
| killall <process_name> | Kill all processes with a specific name | $ killall firefox |
| df -h | Disk usage per filesystem (human-readable) | $ df -h |
| du -sh <directory> | Size of a directory | $ du -sh /home/user/Documents |

Notes
- Use man <command> to get the manual/help for any command.Example: $ man ls
- Many commands require sudo for administrative privileges.Use sudo when a command needs admin privileges.    


