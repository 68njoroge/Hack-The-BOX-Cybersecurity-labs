# Project Documentation: Web Application Security Audit & S3 Bucket Misconfiguration Exploitation (HTB: Three)

## 1. What Problem Does It Solve?
Modern web architectures frequently rely on cloud storage buckets to host static assets, images, and user uploads. However, if access control policies are misconfigured, these public cloud resources can become entry points for unauthorized infrastructure access.

This project addresses the operational risks associated with **Insecure Cloud Storage Bucket Policies leading to Remote Code Execution (RCE)**. It demonstrates how an attacker can leverage virtual host fuzzing to discover hidden subdomains hosting storage infrastructure, exploit public write permissions to upload a malicious PHP web shell, and execute arbitrary commands on the underlying web server. Documenting this attack vector allows organizations to analyze access control lists (ACLs) and implement secure cloud architecture standards.

---

## 2. Tools Used
* **Linux OS (Ubuntu/Kali):** The local host deployment framework utilized to manage, configure, and execute the assessment infrastructure.
* **ffuf (v2.1.0-dev):** A high-performance web fuzzing utility used to conduct virtual host discovery and map target subdomains.
* **AWS CLI:** The official Amazon Web Services command-line tool, utilized to interact with and exploit the exposed S3 storage bucket.

---

## 3. What I Did (The Methodology)
I executed a structured three-phase penetration testing methodology against the targeted web environment:
1. **Virtual Host Fuzzing & Enumeration:** Leveraged a local common discovery wordlist combined with custom host headers to fuzz the target domain (`http://thetoppers.htb`). This eliminated blind guessing and successfully isolated the infrastructure subdomain routing schema.
2. **Cloud Storage Asset Assessment:** Identified an exposed Amazon S3 bucket instance, probed its public access control list configurations using anonymous authentication, and mapped the web-accessible root directory directory structure.
3. **Exploitation & Remote Command Execution:** Uploaded a customized PHP interactive web shell directly into the vulnerable cloud storage bucket, mapped the file execution path via the web browser, and triggered remote system commands to extract the target machine flag token.

---

## 4. What I Found (The Vulnerabilities Discovered)
* **Unprotected Virtual Host Routing:** The routing configuration allowed arbitrary subdomains to resolve, leaving internal service pointers exposed to external enumeration.
* **Globally Insecure S3 Bucket Permissions:** The underlying storage bucket lacked explicit Identity and Access Management (IAM) restriction policies, granting global, unauthenticated read/write access to arbitrary users.
* **Arbitrary File Upload to Web Root:** Because the S3 storage bucket was directly bound to a public web-accessible directory, uploading file assets allowed immediate, unvalidated backend PHP execution on the server host.

---

## 5. What I Learned
* **The Criticality of Vhost Discovery:** Standard IP and directory scanning frequently overlook modular application structures. Subdomain fuzzing provides visibility into hidden cloud resource interfaces connected to the primary domain.
* **Cloud Infrastructure Interoperability:** Understood how modern web applications closely interface with object storage solutions and how a failure in cloud asset access control directly translates into a complete web server compromise.
* **Web Shell Weaponization Mechanics:** Gained operational experience utilizing minimal, lightweight PHP logic components to establish communication channels with remote operating system layers.

---

## 6. Skills I Developed
* **Subdomain Fuzzing Automation:** Implementing `ffuf` payload structures (`-w`, `-u`, `-H`) to inspect unique HTTP response patterns and filter out false positives based on file size variations.
* **Object Storage Exploitation:** Mastering the manipulation of unauthenticated cloud storage utilities using raw CLI syntax structures (`aws s3 ls`, `aws s3 cp`) directed at arbitrary remote endpoints.
* **Arbitrary Remote Code Delivery:** Structuring server-side script triggers to bypass access verification loops and force runtime command translation.

---

## 7. Evidence of Completion (Proof of Work)

To verify the successful exploitation and execution of this security audit, the following live terminal screenshots were captured during the active testing window:

### Virtual Host Discovery
The screenshot below documents the execution of the `ffuf` virtual host fuzzing scan. Using the `common.txt` wordlist targeting `http://thetoppers.htb`, the tool successfully isolated active subdomain structures by filtering out baseline response profiles:

![ffuf Subdomain Fuzzing](./ffuf.png)

### Storage Exploration & Flag Capture
The second screenshot showcases the configuration layout of the compromised environment. By directly interfacing with the exposed web presentation layers, the administrative control panels were verified and leveraged to read out the operational host machine flag:

![Web Application Panel](./web_v.png)

---

## 8. Hardening & Remediation Recommendations
To protect cloud-integrated web portals against asset injection vectors in production environments, implement the following security mitigations:
1. **Enforce Least Privilege S3 Bucket Policies:** Restrict bucket access permissions by explicitly disabling public read/write capabilities. Use robust IAM policies and bucket access control lists (ACLs) that only permit authenticated, internal application service accounts.
2. **Disable Execution Permissions in Upload Directories:** Configure the web server hosting environment (e.g., Apache or Nginx) to completely block script execution flags (`.php`, `.phtml`, `.exe`) within folders serving static assets or user uploads.
3. **Implement Strict Inbound Subdomain Filtering:** Configure edge routers and DNS reverse-proxies to drop or tightly restrict arbitrary wildcard subdomains, routing unauthenticated public connections exclusively to designated edge gateways.