# 🛡️ Nginx + Wazuh SIEM/XDR Security Monitoring Lab

![Wazuh](https://img.shields.io/badge/Wazuh-SIEM%2FXDR-blue?style=flat-square&logo=wazuh)
![Nginx](https://img.shields.io/badge/Nginx-Web%20Server-green?style=flat-square&logo=nginx)
![Ubuntu](https://img.shields.io/badge/OS-Ubuntu%20Linux-E95420?style=flat-square&logo=ubuntu)
![Status](https://img.shields.io/badge/Status-Active%20Lab-brightgreen?style=flat-square)

Hands-on cybersecurity project focused on configuring web server log collection, analyzing HTTP traffic, and integrating **Nginx** with **Wazuh SIEM/XDR** for real-time security event detection.

---

## 🎯 Project Goals

- Deploy and configure an **Nginx** web server on Ubuntu Linux.
- Set up **Wazuh Agent** to forward web server access and error logs to **Wazuh Manager**.
- Practice reading and analyzing HTTP log formats (`access.log`, `error.log`).
- Detect suspicious web requests, non-existent path scanning (404 errors), and potential web attacks in real time.
- Gain practical experience with Security Information and Event Management (SIEM) workflows.

---

## 🏗️ Lab Architecture

```text
[ Client / Tester ]
        │
        │ HTTP Requests (curl / browser)
        ▼
[ Nginx Web Server ]  ---> /var/log/nginx/access.log & error.log
        │
        │ Log Collector (<localfile> XML module)
        ▼
 [ Wazuh Agent ]
        │
        │ Encrypted log stream (TCP Port 1514/1515)
        ▼
 [ Wazuh Manager ]  ---> Rule Engine & Security Analytics
        │
        ▼
[ Wazuh Dashboard ] (Visual Alerts, Rule Triggers, Log Analysis)
```

---

## ⚙️ Configuration Details

### 1. Nginx Log Collection Setup
To monitor web server activity, the Wazuh Agent configuration (`/var/log/nginx/ossec.conf` or `/var/ossec/etc/ossec.conf`) was updated with the following `<localfile>` blocks:

```xml
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/nginx/access.log</location>
</localfile>

<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/nginx/error.log</location>
</localfile>
```

### 2. Service Management
Restarted the Wazuh Agent service to apply configuration changes:

```bash
sudo systemctl restart wazuh-agent
```

---

## 🧪 Experiments & Validation

### Generating Test Traffic
A test HTTP request was generated toward a non-existent endpoint to verify log capturing and alert parsing:

```bash
curl "[http://127.0.0.1/test-wazuh-log](http://127.0.0.1/test-wazuh-log)"
```

### Security Alert Verification (Wazuh Dashboard)
The log entry was successfully read by Wazuh Agent, forwarded to Wazuh Manager, and displayed in **Wazuh Discover / Security Events**:

- **Log Location:** `/var/log/nginx/access.log`
- **Request URI:** `/test-wazuh-log`
- **HTTP Status Code:** `404` (Not Found)
- **Triggered Rule:** `31101` — *Web server 400 error code*
- **Alert Level:** `5`

---

## 📈 Key Takeaways

1. **Log Forwarding:** Understood how endpoint agents read local log files asynchronously and stream them to a centralized SIEM platform.
2. **Rule Matching:** Observed how raw log strings (`127.0.0.1 - - [05/Oct/2026:22:34:20] "GET /test-wazuh-log HTTP/1.1" 404 ...`) are parsed by decoders and matched against Wazuh detection rules.
3. **Troubleshooting:** Resolved agent registration issues, connection timeouts (`Port 1515/1514`), and system configuration mismatches.

---

## 📌 Future Improvements

- [ ] Simulate web application attacks (SQL Injection, Path Traversal, Directory Fuzzing).
- [ ] Create custom Wazuh rules to alert on specific request anomalies.
- [ ] Implement Nginx security hardening (limiting HTTP methods, adding security headers).
