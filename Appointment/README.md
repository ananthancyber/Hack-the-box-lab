
# Hack The Box - Appointment

![Difficulty](https://img.shields.io/badge/Difficulty-Very%20Easy-success)
![Category](https://img.shields.io/badge/Category-Web%20Application-blue)
![Platform](https://img.shields.io/badge/Platform-Hack%20The%20Box-black)

## Overview

The **Appointment** machine introduces the fundamentals of **Web Application Penetration Testing**. The objective is to enumerate the target, identify the exposed service, investigate the web application, and exploit a vulnerable authentication mechanism caused by improper SQL query construction.

---

# Skills Learned

- Network Enumeration
- Service Enumeration
- Web Enumeration
- Directory Enumeration
- SQL Injection Fundamentals
- Authentication Bypass
- Web Application Reconnaissance

---

# Tools Used

- Nmap
- Gobuster
- Firefox
- Kali Linux

---

# Target Information

| Item | Value |
|------|-------|
| Target IP | `10.129.130.166` |
| Platform | Hack The Box Academy |
| Difficulty | Very Easy |

---

# Step 1 - Verify VPN Connectivity

Before interacting with the target, verify that the VPN tunnel is active.

```bash
ip a
```

The VPN interface (`tun0`) confirms connectivity to the Hack The Box network.

```
tun0
inet 10.10.14.84/23
```

---

# Step 2 - Service Enumeration

Perform an initial scan to identify exposed services.

```bash
sudo nmap -sC -sV 10.129.130.166
```

## Scan Result

```
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.38
```

### Findings

- Only TCP Port **80** was open.
- Apache HTTP Server version **2.4.38** was identified.
- Since only HTTP was exposed, the attack surface was limited to the web application.

---

# Step 3 - Web Enumeration

Open the target inside the browser.

```
http://10.129.130.166
```

The application presented a login page requiring:

- Username
- Password

No additional functionality was visible.

---

# Step 4 - Directory Enumeration

Enumerate hidden resources using Gobuster.

```bash
gobuster dir \
-u http://10.129.130.166 \
-w /usr/share/wordlists/dirb/common.txt
```

## Results

```
css
fonts
images
index.php
js
vendor
```

### Interesting Responses

| Directory | Status |
|-----------|--------|
| css | 301 |
| fonts | 301 |
| images | 301 |
| js | 301 |
| vendor | 301 |
| index.php | 200 |
| .htaccess | 403 |
| .htpasswd | 403 |
| server-status | 403 |

### Analysis

The enumeration discovered only standard web application resources.

No administrative panels, backup files, or hidden endpoints useful for exploitation were identified.

---

# Step 5 - Authentication Testing

Several common default credentials were tested.

Examples included:

```
admin : admin

guest : guest

user : user

root : root
```

None of the default credentials resulted in successful authentication.

---

# Step 6 - Vulnerability Assessment

Since directory enumeration did not reveal additional attack vectors and default credentials failed, attention shifted toward the login form.

The application was vulnerable to **SQL Injection**, allowing the authentication query to be manipulated and authentication to be bypassed.

The vulnerability existed because user input was incorporated directly into the SQL query without proper parameterization or input handling.

---

# Step 7 - Exploitation

Using the SQL Injection vulnerability, authentication was bypassed successfully.

After successful authentication, the application returned the challenge completion page.

> **Note:** The challenge flag has been intentionally omitted to comply with Hack The Box content guidelines.

---

# Concepts Learned

## Enumeration

Enumeration is the process of gathering as much information as possible before attempting exploitation.

This assessment included:

- Network Enumeration
- Service Enumeration
- Web Enumeration
- Directory Enumeration

---

## HTTP

- HTTP uses Port 80 by default.
- Browsers communicate with web servers using HTTP requests and responses.
- Web applications often expose authentication portals over HTTP.

---

## SQL

Structured Query Language (SQL) is used to interact with relational databases.

Typical applications use SQL for:

- User authentication
- Data retrieval
- Data storage

---

## SQL Injection

SQL Injection occurs when user input is improperly incorporated into SQL statements.

If applications fail to use parameterized queries, attackers may be able to manipulate database queries, bypass authentication, or retrieve unintended data.

Modern applications mitigate SQL Injection through:

- Parameterized Queries
- Prepared Statements
- Input Validation
- Stored Procedures

---

# Key Takeaways

- Always begin with enumeration.
- Service version detection helps identify the attack surface.
- Directory enumeration is useful even when it yields only standard resources.
- Login portals should be assessed carefully for common web application vulnerabilities.
- SQL Injection remains a critical vulnerability when secure coding practices are not followed.

---

# References

- Hack The Box Academy
- OWASP Top 10
- Nmap Documentation
- Gobuster Documentation

---

## Disclaimer

This walkthrough documents activities performed exclusively inside the **Hack The Box Academy** training environment for educational purposes. No systems outside the authorized lab environment were targeted.
