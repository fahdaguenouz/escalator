## Escalator

### Introduction

Privilege escalation is a fundamental concept in cybersecurity, allowing attackers to elevate their privileges on a system to gain higher levels of access. In this project, you will explore and understand privilege escalation techniques using a virtual machine (VM) designed for this challenge. The goal is to gain root access to the system and retrieve a hidden flag.

### Why This Project Matters

Privilege escalation vulnerabilities are among the most critical security issues in any system. Understanding how attackers exploit these weaknesses is essential for building secure infrastructure. This project teaches you to "think like an attacker" so you can:

- **Build Better Defenses**: By understanding escalation techniques, you can design systems with proper access controls and least-privilege principles.
- **Conduct Effective Security Assessments**: Penetration testers must identify privilege escalation paths to demonstrate the full impact of vulnerabilities.
- **Improve Incident Response**: When a breach occurs, understanding how attackers escalate privileges helps you contain threats and remediate faster.
- **Write Better Vulnerability Reports**: Learning to document findings professionally is essential for bug bounty programs and security consulting.

The knowledge gained here should be used to **protect organizations, improve security posture, and conduct authorized security testing** — never for malicious purposes.

### Objective

The goal of this project is to help you gain hands-on experience with privilege escalation. You will install a provided VM locally, identify the IP address, enumerate the system for potential vulnerabilities, and ultimately escalate your privileges from a regular user to root.

By completing this project, you will:

- Develop a practical understanding of privilege escalation in Linux systems.
- Learn to identify and exploit vulnerabilities that can lead to unauthorized access.
- Gain experience in ethical hacking and penetration testing methodologies.
- Understand the importance of securing systems against privilege escalation attacks.
- Learn how to write professional vulnerability reports for bug bounty programs.

### Role Play

As part of the project, you will participate in a role-play session where you will act as a **Penetration Tester** presenting your findings to a hypothetical team of stakeholders. Prepare to discuss:

- How you identified and leveraged the vulnerabilities.
- The impact these vulnerabilities could have in real-world scenarios.
- Recommendations for securing the system against privilege escalation.
- The importance of responsible disclosure and ethical hacking practices.
- How organizations can detect and prevent privilege escalation attacks.

### Project Requirements

#### System Requirements

**For Intel/AMD Systems (VirtualBox):**

- VirtualBox 6.0 or later
- Minimum 4GB RAM available for the VM (8GB host RAM recommended)
- 20GB free disk space
- 64-bit processor with virtualization support enabled

**For Apple Silicon (M1/M2/M3/M4) - UTM:**

- UTM (available free from [Mac App Store](https://apps.apple.com/app/utm-virtual-machines/id1538878817) or [getutm.app](https://getutm.app))
- Minimum 4GB RAM available for the VM (8GB host RAM recommended)
- 20GB free disk space

#### Setup and Installation

Download the provided VM image and set it up:

**For Intel/AMD Systems:**

1. Download the [VM Image - OVA Format](https://assets.01-edu.org/cybersecurity/local/01-Local.ova)
2. Verify the SHA1 checksum: `f3422f3364fd38e8183740f8f57fa951d3f6e0bf`
3. Open VirtualBox and import the OVA file (File → Import Appliance)
4. **Configure network settings to "Host-Only Adapter" or "Bridged Adapter"** (required for network scanning)
5. Start the VM

> **Important:** Do NOT use "NAT" network mode. With NAT, your host machine cannot directly scan the VM with tools like `nmap`, `arp-scan`, or `netdiscover`. You must use "Host-Only Adapter" or "Bridged Adapter" for proper network connectivity.

**For Apple Silicon (M1/M2/M3/M4):**

1. Download the [VM Image - UTM Format](https://assets.01-edu.org/cybersecurity/local/01-Local1.utm.zip)
2. Verify the SHA1 checksum: `d4a40ca50044778ddc01a57ac16382e4140000e0`
3. Extract the ZIP file
4. Open UTM and import the VM (click + → Open Existing)
5. **Configure network mode to "Bridged" or "Host Only"** (required for network scanning)
6. Start the VM

> **Important:** Do NOT use "Shared Network" (NAT) mode in UTM. You must use "Bridged" or "Host Only" network mode to scan the VM from your host machine.

**Verifying Checksums:**

On Linux/macOS:

```bash
sha1sum 01-Local.ova
# or
shasum 01-Local1.utm.zip
```

On Windows (PowerShell):

```powershell
Get-FileHash -Algorithm SHA1 01-Local.ova
```

Ensure your virtualization software (VirtualBox or UTM) is properly configured for the audit.

#### The Challenge

- **Achieve Root Access**: Your main objective is to escalate privileges and gain root access.
- **Find the Flag**: Once root access is achieved, locate and display the flag in `/root/root.txt`.
- **Document Your Process**: Keep thorough notes on your methodology for reporting purposes.

#### Guidance

- **IP Discovery**: Use network scanning tools to identify the IP address of the VM.
- **Enumeration**: Leverage system enumeration tools to gather information on running processes, services, and configurations.
- **Privilege Escalation**: Investigate user permissions, files, and services that could be used for privilege escalation.

#### Vulnerability Report Email

Draft an email as if reporting the vulnerability through a bug bounty program. The report should show proof of root access and include the flag with clear reproduction steps.

**Your report must include:**

- **Subject Line**: "Security Vulnerability Report: Privilege Escalation in [System/Environment Name]"
- **Summary**: A concise description of the vulnerability (what it is, not how to exploit it).
- **Steps to Reproduce**: Clear, numbered steps that allow someone else to replicate the vulnerability. This section must be separate from the summary and detailed enough for an auditor to follow.
- **Impact**: The potential risk and implications of the vulnerability.
- **Proof of Root Access**: Indicate root access and display the flag with supporting evidence.

**Example Email Template:**

---

**To**: security@[organization].com  
**Subject**: Security Vulnerability Report: Privilege Escalation in [System/Environment Name]

**Dear Security Team,**

I am writing to report a potential security vulnerability identified during an educational penetration testing exercise. The vulnerability involves privilege escalation within [System/Environment Name - e.g., "Escalator Challenge VM"]. Below are the details:

**Summary**:  
The vulnerability allows an authenticated user to escalate their privileges to root due to [brief reason, e.g., misconfigured file permissions or an exploitable SUID binary].

**Steps to Reproduce**:

1. [First step - e.g., "SSH into the target machine as user 'guest' with password 'guest'"]
2. [Second step - e.g., "Run 'find / -perm -4000 2>/dev/null' to identify SUID binaries"]
3. [Third step - e.g., "Identify vulnerable binary at /usr/bin/example"]
4. [Fourth step - e.g., "Execute the following command to spawn a root shell: ..."]
5. [Fifth step - e.g., "Read the flag with 'cat /root/root.txt'"]

**Impact**:  
If exploited, this could lead to unauthorized root access, compromising the system's integrity and exposing sensitive data.

**Proof of Root Access**:  
I successfully gained root access and retrieved the flag: `[INSERT_FLAG_HERE]`

[Insert screenshot showing root shell prompt and flag content from /root/root.txt]

Please feel free to reach out for further details or clarification.

**Best regards**,  
[Your Name]  
[Your Contact Information]

---

### Documentation

Create a `README.md` file that contains the following:

- **Walkthrough**: Describe the step-by-step process of how you exploited the vulnerability.
- **Remediation**: Suggest ways to fix or mitigate the vulnerability.
- **Vulnerability Report Email**: Include your drafted report following the template above (with Summary, Steps to Reproduce, Impact, and Proof of Root Access).
- **Ethical Hacking Report**: Discuss the ethical responsibilities when performing security testing. This report should cover the following points:

1. The importance of obtaining proper authorization before testing.
2. The legal and ethical boundaries of vulnerability testing.
3. How to report vulnerabilities responsibly and avoid causing harm.

### Bonus

If you complete the mandatory part successfully, and you still have free time, you can implement anything that you feel deserves to be a bonus, for example:

- **Exploring Alternative Methods**: Document different privilege escalation paths or techniques that could achieve the same result.
- **Post-Exploitation Analysis**: Explain potential next steps after gaining root access, such as lateral movement or data exfiltration scenarios.
- **Persistence Mechanisms**: Demonstrate how an attacker could maintain access after gaining root (e.g., adding an SSH key to `/root/.ssh/authorized_keys`, creating a backdoor user, or installing a cron job). Explain how defenders can detect and prevent these techniques.
- **Security Hardening Recommendations**: Provide a comprehensive security hardening guide for the system.
- **Automated Enumeration Script**: Create a script that automates the enumeration process.

Challenge yourself!

### Ethical and Legal Considerations

This project is for educational purposes only. The skills you learn here are the same skills used by:

- **Penetration Testers** who help organizations identify weaknesses before attackers do.
- **Red Team Professionals** who simulate real-world attacks to test defenses.
- **Security Auditors** who assess systems for compliance and security posture.
- **Incident Responders** who need to understand attack techniques to investigate breaches.

You are responsible for following ethical hacking guidelines and only performing security testing in the provided VM environment. Do not use these techniques on unauthorized systems.

> **Disclaimer**: This project is for educational purposes only. All testing must be done ethically and following legal standards. Unauthorized use of these techniques is prohibited and may be illegal.

### Submission and Audit

Submit the following:

- `README.md` with your walkthrough and vulnerability report email.
- Any scripts or files used during the project.

Ensure VirtualBox (for Intel/AMD systems) or UTM (for Apple Silicon) is installed and properly configured for the audit. Your VM must be configured with "Host-Only Adapter" or "Bridged Adapter" network mode.

### Resources

Some useful resources:

- [Privilege Escalation](https://en.wikipedia.org/wiki/Privilege_escalation): Overview of privilege escalation concepts.
- [Nmap](https://nmap.org/): A powerful network scanning tool for discovering hosts and services.
- [Dirsearch](https://github.com/maurosoria/dirsearch): A web path scanner useful for directory enumeration.
- [GTFOBins](https://gtfobins.github.io/): A curated list of Unix binaries that can be used to bypass security restrictions.
- [LinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS): Linux Privilege Escalation Awesome Script for automated enumeration.
- [Linux Privilege Escalation](https://delinea.com/blog/linux-privilege-escalation): Comprehensive guide to Linux privilege escalation techniques.
- [MITRE ATT&CK - Privilege Escalation](https://attack.mitre.org/tactics/TA0004/): Understanding privilege escalation tactics for better defense.
