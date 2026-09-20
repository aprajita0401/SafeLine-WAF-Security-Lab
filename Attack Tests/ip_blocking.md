# Source IP Blocking

## Objective

To configure a custom SafeLine WAF deny rule and verify that traffic from a specified source IP can be blocked.

## Lab Setup

* **Source:** Kali Linux
* **Target:** DVWA
* **WAF:** SafeLine WAF
* **Feature:** Allow/Deny
* **Action:** Block / Deny

## Test Flow

```text
Kali Linux
    ↓
HTTP Request
    ↓
SafeLine WAF
    ↓
Source IP Inspection
    ↓
Deny Rule
    ↓
Request Blocked
```

## Configuration

A custom deny rule was configured in SafeLine to block the source IP address of the Kali Linux testing machine.

Example:

```text
Source IP: <KALI-IP>
Action: Block / Deny
```

The actual lab IP address can be replaced with the IP used in the test environment.

## Testing

After applying the deny rule, an HTTP request was sent from Kali Linux to the SafeLine-protected DVWA application.

The request was inspected by SafeLine and matched the configured source-IP deny rule.

## Result

**Status:** Blocked

* Source IP identified by SafeLine
* Custom deny rule matched the incoming request
* Request was blocked
* Backend DVWA application was not accessed through the blocked request

## Security Observation

The test demonstrated how source-IP-based access control can be used to block traffic from known or unauthorized sources before requests reach the backend application.
