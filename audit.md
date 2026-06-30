#### General

##### Check the Repo Content

Files that must be present in the repository:

- Detailed documentation in the `README.md` file containing the walkthrough and vulnerability report email.
- Any scripts or files created or used during the project.

###### Are all the required files present?

##### Verify Setup

Confirm that the student has the proper virtualization environment configured:

**For Intel/AMD Systems:**

1. Verify VirtualBox is installed and running.
2. Confirm the VM is configured with **"Host-Only Adapter" or "Bridged Adapter"** network mode (NOT NAT).
3. Verify the student can launch and access the VM.

**For Apple Silicon (M1/M2/M3/M4):**

1. Verify UTM is installed and running.
2. Confirm the VM is configured with **"Bridged" or "Host Only"** network mode (NOT Shared Network/NAT).
3. Verify the student can launch and access the VM.

> **Note:** If the VM is configured with NAT, network scanning tools like `nmap`, `arp-scan`, and `netdiscover` will not work properly from the host machine.

###### Is the appropriate virtualization software (VirtualBox or UTM) installed?

###### Is the VM configured with the correct network mode (Host-Only/Bridged, NOT NAT)?

###### Can the VM be launched successfully and is it accessible from the host?

##### Play the Role of a Stakeholder

Conduct a simulated scenario where the student plays the role of a **Penetration Tester** presenting their findings to a team of stakeholders (auditors). Evaluate their understanding, communication skills, and depth of knowledge.

Suggested questions include:

- What steps did you take to identify and escalate privileges?
- Can you explain the impact of the vulnerabilities you found?
- How would you recommend mitigating or fixing these vulnerabilities?
- What tools and techniques did you use for enumeration and privilege escalation?
- How did you ensure that your testing adhered to ethical standards?
- How can organizations detect and prevent this type of privilege escalation?

###### Did the student demonstrate a thorough understanding of the project and concepts?

###### Was the student able to communicate effectively and explain their findings?

###### Did the student discuss the potential real-world impact of the vulnerabilities?

###### Was the student able to justify the security measures and recommendations they provided?

##### Review the Student Documentation

Verify that the `README.md` file contains:

- **Walkthrough**: Describes the step-by-step process of how the vulnerability was exploited.
- **Remediation**: Suggests ways to fix or mitigate the vulnerability.
- **Vulnerability Report Email**: Includes the drafted report following the template (with Summary, Steps to Reproduce, Impact, and Proof of Root Access).
- **Ethical Hacking Report**: Discusses the ethical responsibilities when performing security testing.

###### Does the `README.md` file include a complete and clear walkthrough?

###### Are remediation and mitigation suggestions included and well-explained?

###### Does the documentation discuss the ethical implications and responsibilities of vulnerability testing?

##### Test the Challenge

Verify that the student can:

1. **Demonstrate IP Discovery**:
   - Confirm that the student used appropriate tools (e.g., nmap, arp-scan, netdiscover) to identify the IP of the VM.
   - Ask them to explain their methodology.

2. **Enumerate the System**:
   - Ask the student to show their enumeration process and findings.
   - Verify they identified relevant services, users, or misconfigurations.

3. **Achieve Root Access**:
   - Ask the student to demonstrate gaining root access on the VM.
   - Verify that they can show the flag located at `/root/root.txt`.
   - Confirm they understand the exploitation technique used.

###### Did the student use appropriate tools for IP discovery?

###### Were the enumeration results relevant and useful for the escalation process?

###### Was the student able to achieve root access and find the flag?

###### Can the student explain the technical details of the privilege escalation method used?

##### Review the Vulnerability Report Email

Ask the student to present their **Vulnerability Report Email**. Ensure that it includes:

- **Summary**: A clear and concise description of the vulnerability (what it is, not how to exploit it).
- **Steps to Reproduce**: Clear, numbered steps that are separate from the summary and detailed enough to follow.
- **Impact**: The potential risk and real-world implications.
- **Proof of Root Access**: Screenshot showing root shell prompt and flag content.

###### Does the email include a clear Summary that accurately describes the vulnerability?

###### Does the email include a separate "Steps to Reproduce" section with clear, numbered steps?

###### Does the report adequately explain the potential impact?

###### Is proof of root access provided with appropriate evidence (flag + screenshot)?

###### Is the email professionally formatted and appropriate for security disclosure?

##### Verify Steps to Reproduce

Using the student's "Steps to Reproduce" section from their vulnerability report, attempt to replicate the privilege escalation.

###### Can the auditor successfully replicate the escalation by following the student's "Steps to Reproduce"?

###### Are the steps clear enough that someone unfamiliar with the specific vulnerability could follow them?

#### Bonus

Evaluate any additional work completed beyond the mandatory requirements:

###### + Did the student explore alternative escalation methods and document them?

###### + Did the student conduct post-exploitation analysis and explain potential next steps?

###### + Did the student demonstrate persistence mechanisms (e.g., adding SSH keys to `/root/.ssh/authorized_keys`, creating backdoor users, or installing cron jobs)?

###### + Can the student explain how defenders can detect and prevent the persistence techniques they demonstrated?

###### + Did the student create useful automation scripts or tools?

###### + Did the student provide comprehensive security hardening recommendations?

###### + Is this project an outstanding project that exceeds the basic requirements?
