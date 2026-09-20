# SafeLine WAF Security Lab

A hands-on cybersecurity home lab demonstrating how a Web Application Firewall (WAF) can protect a vulnerable web application from SQL injection, excessive HTTP requests, and unauthorized source IPs.

The lab was built using **VirtualBox, Kali Linux, Ubuntu Server, Docker, DVWA, MariaDB, and SafeLine WAF**.

---

## 📌 Project Overview

The objective of this project was to build a small web application security lab and understand how a WAF detects, monitors, and blocks malicious HTTP traffic before it reaches the backend application.

### Objectives

* Deploy **DVWA (Damn Vulnerable Web Application)** on Ubuntu.
* Use **Kali Linux** to perform controlled web security testing.
* Deploy **SafeLine WAF** as a reverse proxy in front of DVWA.
* Demonstrate **SQL injection detection and blocking**.
* Configure **HTTP flood protection using rate limiting**.
* Configure **custom deny rules** to block a specific source IP.
* Monitor WAF events and understand how malicious requests are handled.

SafeLine is a self-hosted WAF/reverse proxy designed to filter, monitor, and block malicious HTTP/S traffic. Its documented capabilities include web attack protection, IP-based rate limiting, anti-bot protection, and access control.

---

## 🏗️ Lab Architecture

```text
                    Cybersecurity Home Lab
                            
┌──────────────────────┐
│      Kali Linux      │
│   Attack / Testing   │
└──────────┬───────────┘
           │
           │ HTTP Requests
           ▼
┌──────────────────────┐
│    SafeLine WAF      │
│    Reverse Proxy     │
│                      │
│ • SQLi Detection     │
│ • Rate Limiting      │
│ • IP Deny Rules      │
└──────────┬───────────┘
           │
           │ Forwarded Traffic
           ▼
┌──────────────────────┐
│    DVWA Application  │
│   Ubuntu + Docker    │
│                      │
│ ┌──────────────────┐ │
│ │ DVWA Container   │ │
│ └──────────────────┘ │
│                      │
│ ┌──────────────────┐ │
│ │ MariaDB Container│ │
│ └──────────────────┘ │
└──────────────────────┘
```

### Traffic Flow

```text
Kali Linux
    ↓
SafeLine WAF :8080
    ↓
DVWA :4280
    ↓
MariaDB
```

SafeLine acts as the reverse proxy between Kali Linux and the DVWA backend.

---

# 🛠️ Technologies & Tools

| Technology         | Purpose                      |
| ------------------ | ---------------------------- |
| VirtualBox         | Virtualization               |
| Kali Linux         | Security testing             |
| Ubuntu Server      | Web application/WAF host     |
| Docker             | Containerization             |
| DVWA               | Vulnerable web application   |
| MariaDB            | DVWA database                |
| SafeLine WAF       | Web application protection   |
| ApacheBench (`ab`) | HTTP request/load testing    |
| HTTP/HTTPS         | Web traffic                  |
| SQL Injection      | Application security testing |

---

# 1. Introduction

This project involved building a complete cybersecurity home lab using VirtualBox, Kali Linux, Ubuntu Server, Docker, DVWA, MariaDB, and SafeLine WAF.

The lab was designed to demonstrate the difference between:

1. Sending requests directly to a vulnerable application.
2. Placing a WAF in front of the application.
3. Testing whether the WAF detects and blocks malicious or excessive traffic.

The primary security tests performed were:

* SQL injection
* HTTP flood/rate limiting
* Custom source-IP blocking

---

# 2. Key Concepts

## Bridged Network Adapter

A bridged network adapter allows a virtual machine to appear as a separate device on the same network as the host, with its own IP address.

This allowed the Kali and Ubuntu virtual machines to communicate with each other during the lab.

---

## DVWA

**Damn Vulnerable Web Application (DVWA)** is an intentionally vulnerable PHP/MySQL web application designed for practicing web application security testing in a controlled environment.

In this project, DVWA was used as the vulnerable backend application protected by SafeLine.

---

## SafeLine WAF

**SafeLine WAF** is a self-hosted Web Application Firewall and reverse proxy.

It sits between the client and backend application and inspects HTTP/S traffic before forwarding legitimate requests to the application.

SafeLine documents protection against attacks including SQL injection, XSS, code injection, SSRF, path traversal, and HTTP flood, along with rate limiting and access-control capabilities.

In this project, SafeLine was specifically tested for:

* SQL injection detection/blocking
* HTTP flood/rate limiting
* Source IP blocking

---

## Docker

Docker is a containerization platform used to package applications and their dependencies into isolated containers.

In this lab, Docker was used to run:

* DVWA
* MariaDB

---

## MariaDB

MariaDB is an open-source relational database management system compatible with MySQL.

It was used as the database backend for DVWA.

---

# 3. Prerequisites

* Host machine with sufficient RAM and storage
* VirtualBox
* Kali Linux VM
* Ubuntu Server VM
* Internet connection
* Basic Linux command-line knowledge
* Basic networking knowledge

Recommended:

* 8 GB+ RAM
* 50 GB+ available storage

---

# 4. Lab Environment Setup

## 4.1 VirtualBox

VirtualBox was installed on the host machine and used to create the Kali Linux and Ubuntu Server virtual machines.

Official website:

https://www.virtualbox.org/

---

## 4.2 Kali Linux VM

Kali Linux was configured as the security-testing machine.

Example configuration:

```text
Name: KaliLinux
Type: Linux
Version: Debian 64-bit
RAM: 2 GB+
Disk: ~20 GB
Network: Bridged Adapter
```

Official download:

https://www.kali.org/get-kali/

---

## 4.3 Ubuntu Server VM

Ubuntu Server was configured as the application and WAF host.

Example configuration:

```text
Name: UbuntuServer
Type: Linux
Version: Ubuntu 64-bit
RAM: 2 GB+
Disk: ~20 GB
Network: Bridged Adapter
```

Official download:

https://ubuntu.com/download/server

---

## 4.4 Bridged Networking

The VMs were configured with **Bridged Adapter** networking so that Kali Linux and Ubuntu Server could communicate over the same network.

The IP address of each VM was obtained using:

```bash
ifconfig
```

or:

```bash
ip addr
```

---

# 5. Ubuntu Server Configuration

## 5.1 System Updates

```bash
sudo apt-get update && sudo apt-get upgrade -y
```

Install required utilities:

```bash
sudo apt-get install -y net-tools openssl
```

---

# 6. Docker and DVWA Deployment

## 6.1 Install Docker

Docker was installed on Ubuntu using the official Docker repository.

After installation, verify:

```bash
sudo docker --version
```

```bash
sudo docker compose version
```

Test Docker:

```bash
sudo docker run hello-world
```

---

## 6.2 Download DVWA

Instead of cloning through Git, DVWA was downloaded as a ZIP archive:

```bash
cd ~
wget https://github.com/digininja/DVWA/archive/refs/heads/master.zip
unzip master.zip
mv DVWA-master DVWA
cd ~/DVWA
```

---

## 6.3 Start DVWA

```bash
sudo docker compose up -d
```

Verify the containers:

```bash
sudo docker compose ps
```

The lab used:

```text
DVWA → Port 4280
MariaDB → Docker container
```

DVWA was then accessed locally through the Ubuntu host.

---

# 7. DVWA Configuration

After starting DVWA:

```text
http://localhost:4280
```

DVWA was initialized through its setup page.

The DVWA security level was set to:

```text
Low
```

This was done specifically to make the intentionally vulnerable SQL injection functionality available for the controlled security test.

---

# 8. Custom Database Data

A test table was created in the DVWA database to provide sample data for SQL injection testing.

Example:

```sql
CREATE TABLE test_users (
    id INT NOT NULL AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL,
    password VARCHAR(50) NOT NULL,
    PRIMARY KEY (id)
);
```

Sample records:

```sql
INSERT INTO test_users (username, password) VALUES
('alice', 'alice123'),
('bob', 'bob123'),
('admin', 'admin123');
```

---

# 9. SafeLine WAF Installation

SafeLine was installed on the Ubuntu Server and accessed through its management interface.

The SafeLine management interface was available on:

```text
https://<Ubuntu-IP>:9443
```

SafeLine was then configured as a reverse proxy in front of DVWA.

---

# 10. SafeLine Application Configuration

The application was configured so that SafeLine received requests on port:

```text
8080
```

and forwarded them to the DVWA backend on:

```text
4280
```

### Traffic flow

```text
Kali
  │
  │ http://<Ubuntu-IP>:8080
  ▼
SafeLine WAF
  │
  │ http://<Ubuntu-IP>:4280
  ▼
DVWA
```

This ensured that requests from Kali passed through SafeLine before reaching DVWA.

---

# 11. SQL Injection Testing

## 11.1 Objective

The objective was to determine whether SafeLine could detect and block SQL injection requests sent to the vulnerable DVWA application.

---

## 11.2 Testing from Kali

DVWA was accessed through the SafeLine-protected port:

```text
http://<Ubuntu-IP>:8080/login.php
```

After logging into DVWA, the security level was set to:

```text
Low
```

The SQL Injection section was then used for testing.

Controlled SQL injection inputs included:

```text
1' AND 1=1#
```

and:

```text
1' OR '1'='1#
```

---

## 11.3 Result

When the SQL injection payload was submitted through the SafeLine-protected application, SafeLine detected and blocked the malicious request.

The request was prevented from reaching the vulnerable backend application.

### Observed flow

```text
Kali
   │
   │ SQL Injection Payload
   ▼
SafeLine WAF
   │
   │ Detection
   ▼
BLOCKED
   X
   │
   └──── DVWA Backend
```

This demonstrated the role of a WAF in inspecting HTTP requests and blocking malicious application-layer traffic.

---

# 12. HTTP Flood Defense

SafeLine's rate-limiting functionality was used to test protection against excessive HTTP requests.

SafeLine documents rate limiting as a mechanism for mitigating DoS attacks, traffic surges, and other abusive traffic by throttling requests that exceed configured limits.

## Configuration

The SafeLine interface was configured under:

```text
HTTP Flood
└── Rate Limiting
```

A controlled request threshold and blocking duration were configured for the lab.

---

## Testing from Kali

ApacheBench was used to generate a controlled number of requests:

```bash
ab -n 100 -c 10 http://<Ubuntu-IP>:8080/login.php
```

Where:

```text
-n 100 → 100 total requests
-c 10  → 10 concurrent requests
```

The SafeLine dashboard was then checked to determine whether excessive requests were detected and blocked.

### Test flow

```text
Kali
  │
  │ Multiple HTTP Requests
  ▼
SafeLine
  │
  │ Rate Threshold
  ▼
Request Blocking / Rate Limiting
```

This demonstrated basic HTTP flood/rate-limiting protection in a controlled lab environment.

---

# 13. Custom Deny Rule

A custom deny rule was also configured to demonstrate source-IP-based access control.

The IP address of the Kali VM was identified and added to SafeLine's deny rules.

Example:

```text
Source IP:
192.168.x.x

Action:
Block / Deny
```

After the rule was applied, requests originating from the specified Kali IP were blocked when accessing the protected application.

### Test flow

```text
Kali IP
   │
   ▼
SafeLine
   │
   │ Source IP matched deny rule
   ▼
BLOCKED
```

This demonstrated how access-control rules can be used to restrict traffic based on source IP.

---

# 14. Security Tests Performed

| Test               | Tool        | SafeLine Feature     | Result         |
| ------------------ | ----------- | -------------------- | -------------- |
| SQL Injection      | Kali / DVWA | WAF attack detection | Blocked        |
| HTTP Flood         | ApacheBench | Rate Limiting        | Tested         |
| Source IP Blocking | Kali        | Custom Deny Rule     | Blocked        |
| XSS                | —           | —                    | **Not tested** |

> **Note:** SafeLine supports XSS detection, but XSS was not performed as part of this lab and is therefore not claimed as a tested result.

---

# 15. Key Skills Demonstrated

* Web Application Security
* Vulnerability Assessment
* SQL Injection Testing
* WAF Configuration
* HTTP Traffic Analysis
* HTTP Flood / Rate Limiting Testing
* Access Control
* IP-Based Blocking
* Reverse Proxy Configuration
* Linux Administration
* Docker & Docker Compose
* Virtual Machine Networking
* Security Event Monitoring
* Basic Web Security Testing

---

# 16. Project Outcome

The lab demonstrated how SafeLine can be positioned as a reverse proxy between a security-testing machine and a vulnerable web application.

The testing showed that:

* SQL injection attempts could be detected and blocked by the WAF.
* Excessive HTTP requests could be controlled using rate limiting.
* Source IPs could be restricted using custom deny rules.
* DVWA could remain isolated behind the WAF instead of being directly exposed to the testing client.

---

# 17. Conclusion

This project provided hands-on experience in deploying and configuring a Web Application Firewall in a controlled cybersecurity lab environment. The lab involved deploying DVWA with Docker and MariaDB, configuring SafeLine as a reverse proxy, testing SQL injection detection and blocking, implementing HTTP flood protection through rate limiting, and configuring custom IP-based deny rules.

Controlled testing from Kali Linux demonstrated how a WAF can inspect HTTP traffic and prevent malicious or excessive requests from reaching the backend application. The project strengthened practical skills in web application security, WAF configuration, HTTP traffic analysis, vulnerability testing, access control, Linux administration, and security monitoring.

---

# ⚠️ Disclaimer

This project was performed in a controlled home-lab environment using intentionally vulnerable software.

DVWA is designed for security training and testing. The techniques demonstrated in this repository should only be performed against systems and applications that you own or have explicit authorization to test.

---

# 📚 References

* [SafeLine WAF](https://github.com/chaitin/SafeLine)
* [DVWA](https://github.com/digininja/DVWA)
* [Kali Linux](https://www.kali.org/)
* [Ubuntu Server](https://ubuntu.com/download/server)
* [VirtualBox](https://www.virtualbox.org/)
* [Docker](https://www.docker.com/)
