# Openvas
![image](https://github.com/user-attachments/assets/e67ff0d6-4773-4f23-a6f8-6ed0e49b3cfc)



# Vulnerability Management Lab with OpenVAS

**Summary and Purpose**  
In this project, I created a secure Azure network and populated it with two virtual machines: one running the OpenVAS Vulnerability Management Scanner and the other running Windows 10. To demonstrate the importance of proper configuration for vulnerability scans, the Windows 10 virtual machine was intentionally made vulnerable by disabling security controls and installing outdated software.

Two types of scans were conducted to illustrate this point. First, an unauthenticated scan was performed on the Windows 10 machine. After completing the unauthenticated scan, a credentialed scan was configured and initiated. Based on the results of the credentialed scan, remediation steps were implemented to address critical vulnerabilities.


---


## Lab Architecture

| Component | Purpose |
|-----------|---------|
| **Azure VNet**           | Isolated network to host lab assets |
| **OpenVAS VM**           | Vulnerability Management Scanner (HOSSTED image, default “developer” config) |
| **Windows 10 Pro VM**    | Target machine; firewall disabled + outdated software installed |
| **Outdated software**    | *Firefox 97.0b5, VLC 1.1.7, Adobe Reader 10.0.0* (chosen for known CVEs) |

---

<!-- img2 -->

## Unauthenticated Scan

1. **Add host** → Windows 10 VM’s private IP.  
2. **Create target** → default settings, **no credentials**.  
3. **Create task** → run scan.

> **Result:** Severity max **5.0 (Medium)**, outdated apps **not detected**—a stark reminder of how little an unauthenticated probe can see.

## Deploy Resources and Configure Virtual Machines for an Unauthenticated Scan

The first resource created in this project was the OpenVAS Vulnerability Management Scanner. Specifically, OpenVas by HOSSTED was utilized with a default developer configuration. While OpenVAS was installed, a Windows 10 Pro virtual machine was created.
To prepare the Windows 10 machine, the firewall was turned off, and outdated versions of Firefox, VLC Media Player, and Adobe Reader were installed. The goal was for the Windows 10 machine to be blatantly vulnerable. Once the firewall was disabled and the vulnerable software was installed, the machine was restarted.
To configure OpenVAS for an unauthenticated scan, the following steps were completed:
> 1. A new host was created using the Windows 10 virtual machine's private IP Address.

<img src="https://github.com/user-attachments/assets/0807411f-de83-400e-88c8-9268f5e99cc7" width="70%" />

<img src="https://github.com/user-attachments/assets/9930096d-42cd-439c-b8ec-991d8500078e" width="70%" />

> 2. A new target was created using the host from the previous step. All other configurations were left as default, and no credentials were provided to OpenVAS.
<img src="https://github.com/user-attachments/assets/9ed25b15-b9b0-4969-8326-9c25c86508a0" width="50%" />

> 3. A new task was created with the target from the previous step. Again, all other configurations were left as default for this scenario.
<img src="https://github.com/user-attachments/assets/d5892731-adc7-4ef3-8611-2a48ba9b746c" width="70%" />

## Results of the Unauthenticated Scan

Due to the scan being unauthenticated, the vulnerabilities found do not accurately reflect the vulnerabilities on the machine. The outdated software on the virtual machine is not reflected in this scan due to the limited capabilities inherent in unauthenticated scans. 
<img src="https://github.com/user-attachments/assets/9db2448b-052e-4445-a2e0-763c4898eed5" width="70%" />

<img src="https://github.com/user-attachments/assets/49f3f1e2-0b7a-4ecd-a7f8-92ffe204d32e" width="70%" />


## Configure Windows 10 for a Credentialed Scan
Several changes were needed to configure the Windows 10 machine for a credentialed scan. The first step was to verify the Domain, Private, and Public profiles for Windows Firewall, which were still disabled from the initial configuration.
The following steps were then completed:
> • Disable User Account Control.  
<img src="https://github.com/user-attachments/assets/6bd79514-6f7d-470f-936e-3bfdd5fb371e" width="70%" />

> • Enable Remote Registry.  
<img src="https://github.com/user-attachments/assets/a68ff251-6e8e-46a5-85bd-8d3b841fb966" width="70%" />

> • Navigate to the Windows Registry and create a new DWORD named `"LocalAccountTokenFilterPolicy"` and set the value to `"1"`.  
<img src="https://github.com/user-attachments/assets/cdca25bf-e429-47bf-8ced-3694c7bcf892" width="70%" />


> •	Restart the virtual machine.


## Configure OpenVAS for a Credentialed Scan
While the Windows 10 machine restarted, the following steps were completed to configure OpenVAS for a credentialed scan:
> 1. Create a new credential by providing the Windows 10 virtual machine's username and password to OpenVAS.  
<img src="https://github.com/user-attachments/assets/031e8bd0-16c8-4a6d-81d1-0ddebeaeacc6" width="60%" />

> 2. Clone the existing target by clicking the sheep icon under "Actions." Edit the cloned target and enable SMB by selecting the credentials created in the previous step.  
<img src="https://github.com/user-attachments/assets/0fb62f61-872d-4dc6-bf56-28585fed7af6" width="60%" />

> 3. Clone the existing task and edit the clone to use the credentialed target created in the previous step.  
<img src="https://github.com/user-attachments/assets/0239dee5-7691-45fd-a3c5-e7a87d508ef4" width="60%" />


## Results of the Credentialed Scan
The difference in vulnerabilities discovered during the unauthenticated and credentialed scans is night and day. The severity rating increased from 5.0 (medium) to 10.0 (high), and the credentialed scan returned 107 additional vulnerabilities.

The credentialed scan allowed OpenVAS to evaluate the system thoroughly, which included identifying vulnerabilities in the outdated software. To learn more about the vulnerabilities, OpenVAS provides a tab for Common Vulnerabilities and Exposures (CVEs). By including CVEs, OpenVAS allows for an easily digestible breakdown of each vulnerability. The breakdown includes a description, the score, vector, references, and remediations. 

<img src="https://github.com/user-attachments/assets/497ff3df-817d-403f-b299-671df94c1507" width="70%" />

<img src="https://github.com/user-attachments/assets/38356b9a-7e42-4cf3-ae7d-ce4ac8145df7" width="70%" />


## Remediation, Verification Scan, and Conclusion
To remediate most of the vulnerabilities found during the credentialed scan, the outdated software was uninstalled from the Windows 10 machine. Another credentialed scan was performed to verify that the implemented remediations resolved the expected vulnerabilities.
Based on the scan, the remediations succeeded, and a downward vulnerability trend was shown. By removing the outdated software, the number of vulnerabilities found by OpenVAS was reduced by 91. 

This project successfully demonstrated the configuration of OpenVAS and the subsequent remediation of vulnerabilities. The project also explained the importance of conducting credentialed scans when possible, as an unauthenticated scan does not accurately reflect the security of a system. Even though additional high-severity vulnerabilities remained on the verification scan, remediating those vulnerabilities was not within this project's scope.
<img src="https://github.com/user-attachments/assets/497ff3df-817d-403f-b299-671df94c1507" width="70%" />

<img src="https://github.com/user-attachments/assets/38356b9a-7e42-4cf3-ae7d-ce4ac8145df7" width="70%" />

## Reflection

This project was a valuable hands-on experience in vulnerability management, from initial scanner setup to remediation and verification. It deepened my understanding of how authenticated vs. unauthenticated scans differ in accuracy and scope, and reinforced the importance of credentialed access when assessing real-world risks.

OpenVAS was the second vulnerability management suite I’ve worked with, following Tenable Nessus. Each tool has broadened my technical perspective and sharpened my ability to evaluate system exposures across different environments.
Looking ahead, I plan to build out additional labs, including:
A Qualys Vulnerability Management environment for enterprise-level comparisons
A refreshed Tenable Nessus lab with new threat scenarios
A mini Active Directory lab to explore domain-based attack surfaces and vulnerability scanning in directory-integrated networks
These experiences are part of my ongoing effort to strengthen my cybersecurity skill set through applied learning and real-world tools.


![image](https://github.com/user-attachments/assets/02c78df2-8843-488e-84fd-786737e5005c)

