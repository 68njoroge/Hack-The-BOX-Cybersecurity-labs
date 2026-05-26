# Lab Documentation: Meow (HTB Starting Point)

## 1. What Problem Does It Solve?
This lab demonstrates the severe security risks associated with leaving legacy administration protocols exposed to the public internet, as well as the danger of deploying systems with default, unhardened administrative credentials.

## 2. Tools Used
* **Nmap:** Used for network scanning, port identification, and service enumeration.
* **Telnet Client:** Used to establish an unencrypted remote terminal connection to the target host.

## 3. What I Did (Methodology)
1. Deployed the target machine via Hack The Box and initiated a connection through the Pwnbox terminal environment.
2. Conducted a port scan using Nmap to discover open doors on the target IP.
3. Identified an active Telnet service and attempted an interactive remote connection.
4. Tested common administrative default usernames at the login prompt to check for credential hardening.

## 4. What I Found
* **Open Port:** Port 23 (Telnet) was open and accepting external connections.
* **Vulnerability:** The system's root administrative account was not secured with a password. 
* **Impact:** Entering the username `root` instantly granted full, unrestricted command-line access to the target operating system.

## 5. What I Learned & Skills Developed
* **Banner Grabbing & Port Scanning:** Developed hands-on proficiency using Nmap to map out active network vectors.
* **Protocol Security Assessment:** Learned why legacy cleartext protocols like Telnet are dangerous (traffic can be sniffed) and why they must be deprecated.
* **Credential Hardening Principles:** Understood the critical importance of disabling empty or default passwords on administrative profiles.

## 6. Artifacts & Proof of Completion
To prove completion without publicly exposing the definitive HTB flag string, the following verification indicators can be cited:
* Successful capture of the system network banner upon connecting via terminal.
* Acquisition of the root terminal shell prompt (`root@meow:~#`).
* A local file transfer receipt or screenshot verification of the `flag.txt` extraction from the root directory.