# SQL Injection Detection & Blocking

## Objective

To test whether SafeLine WAF can detect and block SQL injection requests sent to the DVWA web application.

## Lab Setup

* **Source:** Kali Linux
* **Target:** DVWA
* **WAF:** SafeLine WAF
* **Backend:** DVWA running on Ubuntu Server
* **Proxy Port:** 8080
* **DVWA Backend Port:** 4280
* **DVWA Security Level:** Low

## Test Flow

```text
Kali Linux
    ↓
SQL Injection Request
    ↓
SafeLine WAF :8080
    ↓
Detection & Blocking
    ↓
DVWA :4280
```

## Test Payload

The following SQL injection payload was used in the DVWA input field:

```text
1' AND 1=1#
```

Another test payload used:

```text
1' OR '1'='1#
```

## Detection & Response

SafeLine WAF inspected the HTTP request and identified the SQL injection pattern.

The malicious request was blocked by SafeLine before it could reach the DVWA backend.

## Result

**Status:** Blocked

* SQL injection request detected by SafeLine WAF
* Malicious request prevented from reaching DVWA
* Security event generated in SafeLine
* Demonstrated WAF-based SQL injection protection

## Security Observation

The test demonstrated how a WAF can inspect incoming HTTP requests and identify malicious input patterns before they reach the vulnerable web application.
