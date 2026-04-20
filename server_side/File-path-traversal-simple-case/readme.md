<img width="741" height="320" alt="{B4BC117A-2526-40C5-B46C-F27D74BDECAA}" src="https://github.com/user-attachments/assets/4a8c12f1-dc80-4e66-a43f-b8f5cdcca87b" />



So welcome to the walkthrough

below are the steps to attempt file path traversal simple case

File Path Traversal — Simple Case (Walkthrough)
Objective

Identify a vulnerable file parameter and exploit directory traversal (../) to access sensitive files outside the intended directory.

Step 1: Generate Traffic
Open the lab in your browser
Make sure Burp Proxy is running
Keep Intercept OFF (important for HTTP history method)
Click on any product image or functionality that loads an image

👉 Goal: Force the application to send file-related requests

Step 2: Open HTTP History
Go to:
Proxy → HTTP history
You will see multiple requests

👉 You are now analyzing captured traffic (passive recon phase)

<img width="804" height="337" alt="{B8E85A48-4467-4EB2-BF8E-2C7478EE4E87}" src="https://github.com/user-attachments/assets/03ac8ea3-1ebe-4389-b690-4211129cabbc" />
 (to faster the search of the image request, we can add filters as well as showen in the below image)
 <img width="1203" height="500" alt="image" src="https://github.com/user-attachments/assets/00ce3115-cde1-45ce-96b3-8a8831372a42" />

Step 3: Identify the Target Request

Look for a request like:

GET /image?filename=218.png HTTP/1.1

Focus points:

Parameter name: filename
File type: .png
Endpoint: /image

👉 This is your attack surface
<img width="1148" height="560" alt="image" src="https://github.com/user-attachments/assets/dfd096f8-9c14-4c0d-8173-36dc132687d6" />

Step 4: Confirm Behavior
Click the request
Check Response tab

You should see:

Image data or rendered image

👉 This confirms the parameter is actually used to fetch files
<img width="887" height="628" alt="{BCB0C839-C726-40CD-A9F5-5FF6A050BF0B}" src="https://github.com/user-attachments/assets/24b045b8-ef9a-4ca9-9eac-ca59e289a5eb" />


Step 5: Send to Repeater
Right-click the request
Click Send to Repeater
Go to Repeater tab

👉 Now you can safely modify and test payloads

Step 6: Test Traversal Payload

Modify this part:

filename=7.jpg

Replace with:

filename=../../../etc/passwd

Full request becomes:

GET /image?filename=../../../etc/passwd HTTP/1.1

<img width="1008" height="653" alt="image" src="https://github.com/user-attachments/assets/5d5732bb-7657-4bf3-9bb7-6a759310f77f" />

after replacing it should look like this

<img width="1075" height="629" alt="image" src="https://github.com/user-attachments/assets/744c7d53-9fb7-478e-8c1c-1882ce8067df" />

Step 7: Send Request
Click Send in Repeater

👉 Observe the response carefully
<img width="1060" height="656" alt="image" src="https://github.com/user-attachments/assets/9484f1b2-8963-4dd3-9f61-9e19d31a89d4" />

Step 8: Verify Exploit

If vulnerable, you will see:

root:x:0:0:root:/root:/bin/bash

Key indicators:

root:x:0:0
Multiple system users listed

👉 This confirms successful path traversal

<img width="1066" height="1024" alt="{56E72987-7F93-4206-A748-148105EACC9D}" src="https://github.com/user-attachments/assets/0696cc9b-09ec-4383-a7c4-07be92c8f1c0" />

Step 9: Understand Why It Worked
Application directly used user input in file path
No validation or sanitization
../ allowed directory escape

<img width="1278" height="295" alt="{416CB55A-74C2-4AF2-977B-99897D4E4203}" src="https://github.com/user-attachments/assets/c0b05c73-eec7-4deb-9629-1cd3992c83ca" />

Lab COMPLETED!!

Impact
Attacker can access sensitive files from the server filesystem
Exposure of critical data such as:
System files (e.g., /etc/passwd)
Application configuration files
Credentials or API keys
Can lead to:
Privilege escalation
Further exploitation
Full system compromise
Mitigation
Implement strict input validation (preferably whitelist-based)
Restrict file access to a predefined directory only
Use canonical path validation to ensure paths do not escape the base directory
Avoid direct use of user input in file system operations
Conclusion
The application is vulnerable to path traversal
Caused by improper handling of user-supplied input
Allows attackers to access sensitive files outside intended directories
Leads to compromise of system confidentiality
Severity Assessment
Severity: High
Reasons:
Direct access to sensitive server files
No authentication required
Potential exposure of credentials and system configurations
CVSS (Approximate)
Vector: AV:N / AC:L / PR:N / UI:N / S:U / C:H / I:N / A:N
Score: ~7.5 (High)

Explanation:

Network exploitable
Low attack complexity
No privileges required
High confidentiality impact
OWASP Mapping
Category: A05: Security Misconfiguration (OWASP Top 10 2021)
Also related to:
Improper Input Validation
Broken Access Control
Real-World Impact
Attackers can read sensitive files such as:
/etc/passwd
Application configuration files (may contain DB credentials)
Can result in:
Credential leakage
Internal system reconnaissance
Chaining with other vulnerabilities (e.g., Remote Code Execution)
In enterprise environments, may lead to full server compromise



