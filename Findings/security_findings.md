# Security Findings

## Overview

This document summarizes the security events and protection mechanisms tested in the SafeLine WAF Security Monitoring & Attack Detection Lab.

All tests were performed in a controlled home-lab environment using Kali Linux as the testing system and DVWA as the vulnerable web application.

## Security Test Summary

| Test               | Source     | Detection / Control        | Action        | Result                                  |
| ------------------ | ---------- | -------------------------- | ------------- | --------------------------------------- |
| SQL Injection      | Kali Linux | SafeLine WAF               | Block         | Malicious SQL injection request blocked |
| HTTP Flood         | Kali Linux | HTTP Flood / Rate Limiting | Limit / Block | Excessive HTTP requests controlled      |
| Source IP Blocking | Kali Linux | Custom Deny Rule           | Block         | Traffic from specified source IP denied |

## Finding 1 — SQL Injection

**Activity:** SQL injection payloads were submitted to the DVWA application.

**Example payload:**

```text
1' AND 1=1#
```

**Detection:** SafeLine WAF identified the request as malicious SQL injection traffic.

**Response:** The request was blocked before reaching the DVWA backend.

**Impact:** Demonstrated that the configured WAF protection can prevent SQL injection requests from reaching the vulnerable application.

**Status:** Mitigated by SafeLine WAF

---

## Finding 2 — HTTP Flood

**Activity:** A controlled HTTP request flood was generated from Kali Linux using ApacheBench.

**Test command:**

```bash
ab -n 100 -c 10 http://<LAB-IP>:8080/login.php
```

**Detection:** SafeLine monitored the incoming HTTP request rate and applied the configured HTTP Flood / Rate Limiting policy.

**Response:** Excessive requests were limited or blocked according to the configured policy.

**Impact:** Demonstrated the use of rate limiting to control excessive HTTP request activity.

**Status:** Mitigated by SafeLine rate limiting

> This was a controlled HTTP flood test and should not be described as a large-scale DDoS attack.

---

## Finding 3 — Unauthorized Source IP

**Activity:** A custom deny rule was configured to block the source IP of the Kali Linux testing system.

**Detection:** SafeLine inspected the source IP of incoming HTTP traffic and matched the configured deny rule.

**Response:** Requests from the specified source IP were blocked.

**Impact:** Demonstrated source-IP-based access control for restricting unwanted traffic.

**Status:** Mitigated by SafeLine IP deny rule

---

## Security Monitoring Flow

```text
Attack / Test Activity
        ↓
HTTP Traffic
        ↓
SafeLine WAF
        ↓
Detection / Rule Match
        ↓
Security Event
        ↓
Blocking / Rate Limiting
        ↓
Backend Protection
```

## Key Observations

* SafeLine WAF successfully inspected traffic between Kali Linux and the DVWA backend.
* SQL injection requests were detected and blocked.
* Controlled HTTP flood activity triggered the configured rate-limiting protection.
* Custom source-IP deny rules successfully restricted traffic from the specified IP.
* Testing confirmed that traffic sent through the SafeLine proxy was subject to the configured security controls.
* The lab provided practical exposure to HTTP traffic analysis, WAF detection, security events, access control, and attack mitigation.

## Skills Demonstrated

* Web Application Security
* WAF Configuration
* HTTP Traffic Analysis
* SQL Injection Detection
* HTTP Flood / Rate Limiting
* IP-Based Access Control
* Security Event Monitoring
* Attack Detection & Mitigation
* Linux Administration
* Security Testing

## Scope & Limitations

The testing was performed only in a controlled home-lab environment.

The project specifically tested:

* SQL Injection
* Controlled HTTP Flood / Rate Limiting
* Source IP Blocking

**XSS was not tested as part of this project. Large-scale DDoS testing was not performed.**
