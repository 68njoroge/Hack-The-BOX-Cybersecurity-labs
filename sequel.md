# Project Documentation: Database Security Audit & Administrative Enumeration (HTB: Sequel)

## 1. What Problem Does It Solve?
In enterprise networks, database management services often handle critical operational data, user credentials, and configurations. If these services are improperly exposed to public-facing interfaces or deployed with default setup configurations, they present a massive attack surface.

This project addresses the operational risks associated with **Insecure Network Service Exposure and Null Authentication Vulnerabilities** in relational database management systems (RDBMS). It demonstrates how a remote attacker can identify open service ports using network scanners and exploit unauthenticated administrative access (`root` user with a blank password) to inspect database structures and exfiltrate sensitive token keys. Documenting these structural oversight loops helps organizations implement hardening standards and restrict database access controls.

---

## 2. Tools Used
* **Linux OS (Ubuntu):** The local host engineering environment on the HP EliteBook utilized to deploy tracking tools and maintain connectivity to the testing infrastructure.
* **Nmap (Network Mapper):** An open-source network exploration and security auditing tool used to run port discovery and service finger-printing.
* **Netcat (nc):** A versatile networking utility used to establish raw TCP socket connections to grab low-level application banners.
* **MariaDB Client:** The native command-line utility used to authenticate, navigate, and query the open relational database management system securely.

---

## 3. What I Did (The Methodology)
I executed a structured three-phase infrastructure security assessment against the target database host:
1. **Infrastructure Service Enumeration:** Initiated high-rate port scanning protocols to sweep all 65,535 TCP ports on the target machine, pinpointing active listening database ports while dealing with high-latency network routes.
2. **Low-Level Banner Grabbing:** Bypassed timing-out scanning scripts by dropping down to raw TCP pipes using Netcat, forcing the remote engine to expose its exact software version, configuration variants, and distribution build.
3. **Privileged Database Authentication Auditing:** Utilized a native terminal database client to attempt administrative validation checks on the remote server using the highest-privileged account user (`root`) without providing a password credential string.

---

## 4. What I Found (The Vulnerabilities Discovered)
* **Public Interface Database Binding:** The remote server was configured to bind its database management daemon directly to an open, public-facing network interface instead of being isolated to a local loopback interface (`127.0.0.1`) or a secure internal management subnet.
* **Administrative Null Authentication (Blank Password):** The primary database administrator account (`root`) was deployed and left active with a blank password value, granting total structural control to any unauthenticated client over the network.
* **Exposed Operational Metadata Tables:** Once authenticated, the database environment permitted global directory lookup reads, exposing non-standard application folders containing unique configuration properties and operational target variables.

---

## 5. What I Learned
* **Mitigating Network Scanning Latency:** Learned how high-latency networks (`~0.29s`) cause complex automated scanning utilities (like Nmap version detection scripts) to drop packets and time out, and how raw socket interaction utilities (like Netcat) cut through network lag to achieve instant output results.
* **Relational Database Structural Navigation:** Gained operational mastery over interactive SQL structures, identifying how to map database environments using `SHOW DATABASES;`, shifting execution focus contexts with `USE <database>;`, and evaluating storage configurations via `DESCRIBE <table_name>;`.
* **Managing Local Client Package Dependencies:** Encountered and solved local application environment constraints on Ubuntu by identifying missing command shortcuts and successfully mapping alternative repository sources to load compatible client packages (`mariadb-client`).

---

## 6. Skills I Developed
* **Advanced Network Reconnaissance:** Running high-performance Nmap flags (`-sV -p- --min-rate`) to map comprehensive network landscapes while interpreting dropped probe variables.
* **Manual Banner Grabbing and Triage:** Opening raw stream interactions across application service endpoints to analyze plain-text initialization handshakes.
* **Interactive Data Audit Manipulation:** Writing clean structured database query language lines directly into terminal environments to read and process backend schema schemas.

---

## 7. Evidence of Completion (Proof of Work)

To verify the successful exploitation, structure mapping, and completion of this database security audit, the following live terminal screenshots were captured during the active testing window:

### Administrative Entry and Schema Discovery
The screenshot below documents the execution of the native terminal connection query. Using the command `mysql -h 10.129.58.246 -u root`, the system successfully established an unauthenticated administrative monitor shell. It also demonstrates running the structural tracking command **`SHOW DATABASES;`**, which maps the structural landscape and uncovers the unique target database **`htb`**:

![MariaDB Client Entry and Schema Mapping](./client.png)

### Table Enumeration and Field Property Inspection
The second screenshot showcases the internal database navigation and layout evaluation. After moving into the target context with `USE htb;` and executing `SHOW TABLES;`, the database exposed two asset tables: `config` and `users`. A deep inspection using **`DESCRIBE config;`** breaks down the table columns, mapping out fields for `id`, `name`, and the data-carrying text engine column **`value`**:

![Table Discovery and Column Description Layout](./describe.png)

---

## 8. Hardening & Remediation Recommendations
To protect relational database platforms against unauthorized entry and structural exposure in enterprise environments, implement the following security mitigations:
1. **Restrict Network Binding Interfaces:** Modify the database configuration file (e.g., `my.cnf` or `50-server.cnf`) to change the `bind-address` directive from listening on all adapters (`0.0.0.0`) to strictly running on the local address frame (`127.0.0.1`) or a dedicated internal management network card interface.
2. **Enforce Strong Password Policies:** Eliminate all empty or null security properties. Secure the default administrative profile immediately using long, randomized password strings by running standard schema modification commands (`ALTER USER 'root'@'localhost' IDENTIFIED BY 'StrongPassword';`).
3. **Implement Network Firewall Rules:** Configure localized system firewalls (`ufw` or `iptables`) or network security group access lists to explicitly drop incoming traffic targeting port `3306` from unauthorized networks, permitting connections solely from validated application servers.