
![image](https://github.com/user-attachments/assets/e67ff0d6-4773-4f23-a6f8-6ed0e49b3cfc)

---

# Openvas

# Vulnerability Management

**Intro**  
In this project, we will create a secure Azure network and populate it with two virtual machines: one running the OpenVAS Vulnerability Management Scanner and the other running Windows 10. To demonstrate the importance of proper configuration for vulnerability scans, the Windows 10 virtual machine was intentionally made vulnerable by disabling security controls and installing outdated software.

Two types of scans were conducted to illustrate this point. First, an unauthenticated scan was performed on the Windows 10 machine. After completing the unauthenticated scan, a credentialed scan was configured and initiated. Based on the results of the credentialed scan, remediation steps were implemented to address critical vulnerabilities.

---

## Lab Architecture

| Component | Purpose |
|-----------|---------|
| **Azure VNet**           | Isolated network to host lab assets |
| **OpenVAS VM**           | Vulnerability Management Scanner (HOSSTED image, default "developer" config) |
| **Windows 10 Pro VM**    | Target machine; firewall disabled + outdated software installed |
| **Outdated software**    | *Firefox 97.0b5, VLC 1.1.7, Adobe Reader 10.0.0* (chosen for known CVEs) |

---

## Unauthenticated Scan

1. **Add host** → Windows 10 VM's private IP.  
2. **Create target** → default settings, **no credentials**.  
3. **Create task** → run scan.

> **Result:** Severity max **5.0 (Medium)**, outdated apps **not detected**—a stark reminder of how little an unauthenticated probe can see.

---

### Deploy Resources and Configure Virtual Machines 

The first resource created for this project was the OpenVAS Vulnerability Management Scanner. Specifically, OpenVAS by HOSSTED was utilized with its default developer configuration. While OpenVAS was installed, a Windows 10 Pro virtual machine was set up.

The firewall was turned off to prepare the Windows 10 machine, and outdated versions of Firefox, VLC Media Player, and Adobe Reader were installed. The goal was to make the Windows 10 machine intentionally vulnerable. The machine was restarted after the firewall was turned off and the vulnerable software was installed.

Environment Setup
> 1. **Add host** → Windows 10 VM’s private IP.  
> 2. **Create target** → default settings, **no credentials**.  
> 3. **Create task** → run scan.

> **Result:** Severity max **5.0 (Medium)**, outdated apps **not detected**—a stark reminder of how little an unauthenticated probe can see.

---

Conduct an unauthenticated scan in OpenVAS:
> 1.  A new host was created using the private IP address of the Windows 10 virtual machine.

<img src="https://github.com/user-attachments/assets/0807411f-de83-400e-88c8-9268f5e99cc7" width="70%" />

> 2. The target was defined and linked to the created host, with all settings left as default and no credentials provided.
<img src="https://github.com/user-attachments/assets/9ed25b15-b9b0-4969-8326-9c25c86508a0" width="50%" />

> 3. The task was initiated using the previously defined target, retaining the default configurations to simulate a basic unauthenticated scan.
<img src="https://github.com/user-attachments/assets/d5892731-adc7-4ef3-8611-2a48ba9b746c" width="70%" />

---

## Unauthenticated Scan  Results

Due to the scan being unauthenticated, the vulnerabilities found do not accurately reflect the vulnerabilities on the machine. The outdated software on the virtual machine is not reflected in this scan due to the limited capabilities inherent in unauthenticated scans. 
<img src="https://github.com/user-attachments/assets/9db2448b-052e-4445-a2e0-763c4898eed5" width="70%" />

---

## Credentialed Scan: Windows 10 Configuration

Several changes were required to configure the Windows 10 machine for a credentialed scan. The first step was to verify that the Domain, Private, and Public profiles for Windows Firewall were still disabled from the initial configuration. The following steps were then completed:
> • Disabled User Account Control (UAC).
<img src="https://github.com/user-attachments/assets/6bd79514-6f7d-470f-936e-3bfdd5fb371e" width="70%" />

> • Enable Remote Registry.  
<img src="https://github.com/user-attachments/assets/a68ff251-6e8e-46a5-85bd-8d3b841fb966" width="70%" />

> • Navigate to the Windows Registry and create a new DWORD named `"LocalAccountTokenFilterPolicy"` and set the value to `"1"`.  
<img src="https://github.com/user-attachments/assets/cdca25bf-e429-47bf-8ced-3694c7bcf892" width="70%" />

> •	Restart the virtual machine.

---

## Credentialed Scan: OpenVAS Configuration

While the Windows 10 machine restarted, the following steps were completed to configure OpenVAS for a credentialed scan:
> 1. Create a new credential by providing the username and password for the Windows 10 virtual machine to OpenVAS.  
<img src="https://github.com/user-attachments/assets/031e8bd0-16c8-4a6d-81d1-0ddebeaeacc6" width="60%" />

> 2. Clone the existing target by clicking the sheep icon under "Actions." Edit the cloned target and enable SMB by selecting the credentials created in the previous step.  
<img src="https://github.com/user-attachments/assets/0fb62f61-872d-4dc6-bf56-28585fed7af6" width="60%" />

> 3. Clone the existing task and edit the clone to use the credentialed target created in the previous step.  
<img src="https://github.com/user-attachments/assets/0239dee5-7691-45fd-a3c5-e7a87d508ef4" width="60%" />

---

## Credentialed Scan Results

The difference in vulnerabilities identified during unauthenticated and credentialed scans is significant. The severity rating increased from 5.0 (medium) to 10.0 (high), and the credentialed scan uncovered 107 vulnerabilities.

The credentialed scan enabled OpenVAS to conduct a thorough evaluation of the system, which included identifying vulnerabilities in outdated software. OpenVAS offers a tab for Common Vulnerabilities and Exposures (CVEs) to provide more information about these vulnerabilities. By including CVEs, OpenVAS presents a clear breakdown of each vulnerability, including a description, severity score, attack vector, references, and suggested remediation steps.

<img src="https://github.com/user-attachments/assets/497ff3df-817d-403f-b299-671df94c1507" width="70%" />

<img src="https://github.com/user-attachments/assets/38356b9a-7e42-4cf3-ae7d-ce4ac8145df7" width="70%" />

---

## Remediation, 

### Verification
To address the vulnerabilities identified during the credentialed scan, we uninstalled the outdated software from the Windows 10 machine. After implementing these changes, we conducted another credentialed scan to verify whether the remediations effectively resolved the identified vulnerabilities.

The results of the follow-up scan indicated that the remediations were successful, showing a downward trend in vulnerabilities. Specifically, by removing the outdated software, we reduced the number of vulnerabilities detected by OpenVAS by 91%.

This project successfully demonstrated the configuration of OpenVAS and the subsequent remediation of vulnerabilities. It also highlighted the importance of conducting credentialed scans whenever possible, as unauthenticated scans do not accurately reflect a system's security posture. Although some high-severity vulnerabilities persisted in the verification scan, addressing those was beyond this project's scope.

<img src="https://github.com/user-attachments/assets/497ff3df-817d-403f-b299-671df94c1507" width="70%" />

<img src="https://github.com/user-attachments/assets/38356b9a-7e42-4cf3-ae7d-ce4ac8145df7" width="70%" />

---

## **Reflection**

This project provided us with valuable hands-on experience in vulnerability management, from initial scanner setup to remediation and verification. It deepened my understanding of the differences between authenticated and unauthenticated scans in terms of accuracy and scope, and reinforced the importance of credentialed access when assessing real-world risks.

OpenVAS was the second vulnerability management suite we've worked with, following Tenable Nessus. Each tool has broadened my technical perspective and sharpened my ability to evaluate system exposures across different environments.

Looking ahead, I plan to build out additional labs, including:
1. A Qualys Vulnerability Management environment for enterprise-level comparisons
2. A refreshed Tenable Nessus lab with new threat scenarios
3. A mini Active Directory lab to explore domain-based attack surfaces and vulnerability scanning in directory-integrated networks

These experiences are part of my ongoing effort to strengthen my cybersecurity skill set through applied learning and real-world tools.

![image](https://github.com/user-attachments/assets/02c78df2-8843-488e-84fd-786737e5005c)

