# Project Documentation: Local File Inclusion & NetNTLMv2 Authentication Capture (HTB: Responder)

## 1. What Problem Does It Solve?
In enterprise Windows network environments, local applications often handle language file parsing or remote data feeds dynamically based on user input. If these parameters are not sanitized, attackers can force the server to communicate with external machines, exposing internal authentication tokens.

This project addresses the operational risks associated with **Local File Inclusion (LFI) mapping to Link-Local Multicast Name Resolution (LLMNR) Poisoning**. It demonstrates how an attacker can leverage an application file inclusion vulnerability to force a remote Windows server to authenticate against a rogue local SMB handler, capture NetNTLMv2 administrative credential hashes, and crack them using rule-based dictionary frameworks. Documenting this attack vector allows organizations to analyze insecure parameter routing and implement robust network authentication standards.

---

## 2. Tools Used
* **Linux OS (Ubuntu/Kali):** The local host deployment framework utilized to manage, configure, and execute the assessment infrastructure.
* **Responder:** A comprehensive LLMNR, NBT-NS, and MDNS poisoner used to spin up rogue authentication listeners and capture NTLM network hashes.
* **John the Ripper (v1.9.0):** A fast, rule-based password cracking tool used to run high-speed dictionary execution profiles against retrieved hash files.
* **Evil-WinRM (v3.9):** A specialized WinRM shell execution client used to establish safe, remote administrative console access upon credential validation.

---

## 3. What I Did (The Methodology)
I executed a structured three-phase penetration testing methodology against the targeted web environment:
1. **LFI Vulnerability Testing:** Probed active dynamic parameter elements on the primary site target (`http://unika.htb`) to confirm a Local File Inclusion flaw capable of handling arbitrary resource calls.
2. **Authentication Interception & Coercion:** Deployed a local network listener via Responder and systematically passed an execution target parameter pointing back to my attacker workstation. This forced the remote server to attempt automated Windows SMB authentication, capturing the administrative NetNTLMv2 hash.
3. **Offline Hash Cracking & Remote Management Takeover:** Transported the captured NetNTLMv2 data block into a localized parsing file, executed an offline dictionary processing attack via John the Ripper to isolate the plaintext password, and initiated an authenticated `evil-winrm` shell to claim full administrative access.

---

## 4. What I Found (The Vulnerabilities Discovered)
* **Unsanitized File Inclusion Vectors:** The `page` parameter within the application web handler directly parses incoming directory pointers without path translation verification or validation filtering.
* **Vulnerability to SMB Credential Relaying:** The host operating system defaults allowed outbound SMB connection requests across public network zones, automatically presenting administrative credential parameters to foreign challenge requests.
* **Weak Enterprise Password Policies:** The administrative profile utilized a predictable, low-entropy credential string (`badminton`) which failed to withstand automated dictionary permutation checks.

---

## 5. What I Learned
* **The Mechanics of NTLM Request Redirection:** Understood how an LFI vulnerability on a Windows backend can be upgraded from a standard file readout flaw into a high-severity authentication coercion mechanism.
* **Dictionary Optimization Strategies:** Gained insight into running focused hash cracking workflows using customized rule profiles against standard global dictionary asset libraries (`rockyou.txt`).
* **Remote Management Protocol Risks:** Observed how exposing WinRM management ports without network segmentation or multi-factor mechanics allows instant host takeovers if individual accounts are compromised.

---

## 6. Skills I Developed
* **Network Hash Interception:** Setting up and executing precise LLMNR/SMB trapping workflows to intercept and write internal domain validation strings.
* **Offline Cryptographic Analysis:** Utilizing `john` with explicit formatting parameters (`--format=netntlmv2`) to crack encrypted network authorization challenge responses.
* **Remote WinRM Environment Exploitation:** Navigating shell payload interfaces on Windows infrastructure targets to perform automated asset analysis.

---

## 7. Evidence of Completion (Proof of Work)

To verify the successful exploitation and execution of this security audit, the following live terminal screenshots were captured during the active testing window:

### Credential Exploitation & Remote Management Shell
The screenshot below documents the ultimate stage of the testing lifecycle. After cracking the NetNTLMv2 hash via `john --show` to reveal the plain-text credential (`badminton`), an authenticated remote administration session is established using `evil-winrm` directly into `unika.htb`:

![John the Ripper Cracking & Evil-WinRM Shell Access](./john_form.jpg)

#

---

## 8. Hardening & Remediation Recommendations
To protect enterprise application services against authentication coercion and file inclusion vectors in production environments, implement the following security mitigations:
1. **Sanitize Dynamic Path Parameters:** Implement strict input whitelisting for all language or file loading parameters in the web application code. Reject any inputs containing directory traversal characters (`../`, `\\`) or external UNC paths.
2. **Restrict Outbound SMB Traffic:** Configure firewall rules to completely block outbound SMB traffic (ports 139 and 445) from the web server to external or untrusted networks, preventing the operating system from sending NTLM hashes to rogue listeners.
3. **Enforce Strong Password and Account Policies:** Implement strict credential complexity regulations across the domain, rendering default dictionary cracking attacks mathematically unfeasible, and disable WinRM access for non-essential administrative profiles.