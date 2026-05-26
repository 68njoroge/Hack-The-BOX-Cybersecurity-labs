# Lab Documentation: Fawn (HTB Starting Point)

## 1. What Problem Does It Solve?
This lab highlights the risks of anonymous file-sharing configurations on network servers. It demonstrates how a simple misconfiguration can allow unauthorized users to explore private directories and exfiltrate sensitive data without authentication.

## 2. Tools Used
* **FTP Client (Built-in Linux utility):** Used to connect to the file server, authenticate, navigate directories, and download files.
* **Nmap (Optional/Alternative):** Used to verify running application versions via service banner grabbing.

## 3. What I Did (Methodology)
1. Discovered an open FTP service on Port 21 of the target IP address.
2. Analysed the service connection banner to identify the underlying software application and version.
3. Attempted an unauthenticated login bypass utilizing the standard "Anonymous" FTP configuration exploit.
4. Interacted with the file system using FTP navigation commands (`ls`) and pulled the target file down locally.

## 4. What I Found
* **Active Service:** FTP running on Port 21.
* **Software Version:** `vsFTPd 3.0.3` (Discovered directly from the service banner text).
* **Vulnerability:** **Anonymous FTP Access** was active. The server accepted the username `anonymous` with a completely blank password.
* **Impact:** Unauthenticated read access allowed me to view the file index and download `flag.txt` directly to my local machine.

## 5. What I Learned & Skills Developed
* **FTP Navigation & Commands:** Mastered interactive file-transfer utilities inside the Linux terminal (`ftp`, `user`, `get`, `ls`, `exit`).
* **Session Management:** Handled real-world terminal scenarios, including managing connection timeouts (`421 Timeout`) and stepping between environments (FTP prompt vs. local bash shell) to execute local commands like `cat`.
* **Access Control Auditing:** Learned how a single configuration line in an application file (`anonymous_enable=YES`) can completely compromise data confidentiality.

## 6. Artifacts & Proof of Completion
To prove completion professionally on a portfolio without revealing the raw flag text:
* **The Connection Banner:** Evidence of the connection string reading `220 (vsFTPd 3.0.3)`.
* **The Authentication Success Receipt:** The server response log reading `230 Login successful`.
* **The File Exfiltration Record:** The terminal download status output reading `226 Transfer complete` for the `flag.txt` target asset.