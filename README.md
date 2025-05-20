# Openvas
![image](https://github.com/user-attachments/assets/e67ff0d6-4773-4f23-a6f8-6ed0e49b3cfc)



# Vulnerability Management Lab with OpenVAS

**Summary and Purpose**  
In this project, I created a secure Azure network and populated it with two virtual machines: one running the OpenVAS Vulnerability Management Scanner and the other running Windows 10. To demonstrate the importance of proper configuration for vulnerability scans, the Windows 10 virtual machine was intentionally made vulnerable by disabling security controls and installing outdated software.

Two types of scans were conducted to illustrate this point. First, an unauthenticated scan was performed on the Windows 10 machine. After completing the unauthenticated scan, a credentialed scan was configured and initiated. Based on the results of the credentialed scan, remediation steps were implemented to address critical vulnerabilities.


---


## 1 — Lab Architecture

| Component | Purpose |
|-----------|---------|
| **Azure VNet**           | Isolated network to host lab assets |
| **OpenVAS VM**           | Vulnerability Management Scanner (HOSSTED image, default “developer” config) |
| **Windows 10 Pro VM**    | Target machine; firewall disabled + outdated software installed |
| **Outdated software**    | *Firefox 97.0b5, VLC 1.1.7, Adobe Reader 10.0.0* (chosen for known CVEs) |

---

<!-- img2 -->

## 2 — Unauthenticated Scan

1. **Add host** → Windows 10 VM’s private IP.  
2. **Create target** → default settings, **no credentials**.  
3. **Create task** → run scan.

> **Result:** Severity max **5.0 (Medium)**, outdated apps **not detected**—a stark reminder of how little an unauthenticated probe can see.

---


## 3 — Preparing for a Credentialed Scan

On the Windows 10 VM  

```text
• Disable User Account Control  
• Enable Remote Registry  
• Add DWORD  LocalAccountTokenFilterPolicy=1  
• Reboot
```


OpenVAS steps

Create credentials (local admin user/pass).

Clone target → attach SMB creds.

Clone task → point to credentialed target.




