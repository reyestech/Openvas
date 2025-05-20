# Openvas
![image](https://github.com/user-attachments/assets/e67ff0d6-4773-4f23-a6f8-6ed0e49b3cfc)



# Vulnerability Management Lab with OpenVAS

**Summary and Purpose**  
In this project, I created a secure Azure network and populated it with two virtual machines: one running the OpenVAS Vulnerability Management Scanner and the other running Windows 10. To demonstrate the importance of proper configuration for vulnerability scans, the Windows 10 virtual machine was intentionally made vulnerable by disabling security controls and installing outdated software.

Two types of scans were conducted to illustrate this point. First, an unauthenticated scan was performed on the Windows 10 machine. After completing the unauthenticated scan, a credentialed scan was configured and initiated. Based on the results of the credentialed scan, remediation steps were implemented to address critical vulnerabilities.


---

<!-- img1 -->

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

<!-- img3 -->

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

<!-- img4 -->
4 — Credentialed Scan Results
Metric	Unauth Scan	Credentialed Scan
Max severity	5.0 (Medium)	10.0 (High)
Total vulns	16	123 (+107)

OpenVAS now reports CVEs for Firefox, VLC, and Adobe Reader, complete with scores, vectors, references, and remediation guidance.

<!-- img5 -->
5 — Remediation & Verification
Action: Uninstalled all outdated software.
Follow-up credentialed scan: Vulnerability count dropped by 91; high-severity items fell sharply.

<!-- img6 -->
6 — Key Takeaways
Credentialed > Unauthenticated: credentials unlock deep file-system & registry checks.

Quick wins matter: removing three legacy apps cut >70 % of findings.

Scan-fix-rescan is essential—never assume remediation worked until verification.

7 — Tech & Standards
Azure: VNet, VMs, NSGs

OpenVAS (GVM): v22.x, default developer template

Windows 10 Pro: 22H2 (baseline)

Regulations referenced: NIST 800-53 (SI-2, CM-8), CIS Benchmark concepts, CVSS v3.1 scoring

8 — Next Steps
Re-create the lab with Tenable Nessus for tooling comparison

Spin up Qualys Vulnerability Management in a new resource group

Build a mini Active Directory environment and scan domain controllers

9 — Author Reflection
“This lab deepened my vulnerability-management skills and highlighted the power of credentialed scans. I’m planning follow-ups with Nessus and Qualys to broaden my toolset.”
— Hector M. Reyes


---

### How to use

1. Copy everything between the ```markdown fences into your `README.md`.  
2. Replace each `<!-- img# -->` comment with the matching image line from your current README.  
3. Commit & push—your repo now has a professional, skimmable portfolio piece.  

Let me know if you’d like any tweaks!


