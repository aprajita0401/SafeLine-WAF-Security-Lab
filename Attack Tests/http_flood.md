# HTTP Flood Detection & Rate Limiting

## Objective

To simulate a controlled HTTP request flood and verify SafeLine WAF's rate-limiting protection.

## Lab Setup

* **Source:** Kali Linux
* **Target:** DVWA
* **WAF:** SafeLine WAF
* **Testing Tool:** ApacheBench (`ab`)
* **Proxy Port:** 8080
* **DVWA Backend Port:** 4280

## Test Flow

```text
Kali Linux
    ↓
ApacheBench HTTP Requests
    ↓
SafeLine WAF :8080
    ↓
Rate Limiting
    ↓
DVWA :4280
```

## Test Command

A controlled HTTP request test was performed against the SafeLine-protected endpoint:

```bash
ab -n 100 -c 10 http://<LAB-IP>:8080/login.php
```

Where:

* `-n 100` = 100 total HTTP requests
* `-c 10` = 10 concurrent requests
* `8080` = SafeLine WAF listening port

> The test was performed against the SafeLine WAF endpoint rather than the DVWA backend port to ensure that the traffic passed through the WAF.

## Detection & Response

SafeLine monitored the incoming request rate and applied the configured HTTP Flood / Rate Limiting protection.

Excessive requests were controlled according to the configured policy.

## Result

**Status:** Rate Limiting / Blocking Applied

* Controlled HTTP request flood generated from Kali Linux
* SafeLine monitored the request rate
* HTTP Flood protection was triggered
* Excessive requests were limited/blocked according to the configured policy

## Security Observation

The test demonstrated how rate limiting can help reduce the impact of excessive HTTP requests and protect a web application from request-flooding activity.

> This was a controlled HTTP flood test in a private home lab and was not a large-scale DDoS test.
