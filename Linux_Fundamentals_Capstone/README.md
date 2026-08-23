# Linux Fundamentals Capstone

## Author

Joseph Apelete Biossey

## Date

August 2026

## Project Overview

This capstone project simulates a small office server environment using Linux administration tools.

The project demonstrates:

- User and Group Management
- Directory and Permission Management
- Nginx Service Administration
- SSH Configuration
- Log Inspection
- File Operations
- Linux Troubleshooting

---

## Environment

| Item | Details |
|--------|---------|
| OS | Ubuntu 26.04 LTS |
| VM Platform | Bio-Lab VM |
| RAM | 3.3 GiB |
| Swap | 3.8 GiB |
| CPU | Intel Core i7-8550U @ 1.80 GHz |
| vCPUs | 2 |
| Architecture | x86_64 |


### System Information Commands

```bash
uname -a
lsb_release -a
free -h
lscpu
```

### Key Findings

- Operating System: Ubuntu 26.04 LTS
- Kernel: 7.0.0-28-generic
- Architecture: x86_64
- Memory: 3.3 GiB RAM
- Virtualization Platform: VMware
- Processor: Intel Core i7-8550U @ 1.80 GHz
- Allocated CPUs: 2
```

---



# 1. System Update and Package Installation

Updated package indexes and installed the tools required for the office server simulation.

## Commands

```bash
sudo apt update
sudo apt install nginx openssh-server git curl tree -y
```

## Verification

### Nginx Version

```bash
nginx -v
```

Output:

```text
nginx version: nginx/1.28.3 (Ubuntu)
```

### OpenSSH Version

```bash
ssh -V
```

Output:

```text
OpenSSH_10.2p1 Ubuntu-2ubuntu3.5, OpenSSL 3.5.5 27 Jan 2026
```

## Result

Successfully installed and verified:

- Nginx
- OpenSSH Server
- Git
- Curl
- Tree

---



# 2. User and Group Management

*To be completed*

---

# 3. Directory Structure

*To be completed*

---

# 4. Permissions Configuration

*To be completed*

---

# 5. Nginx Setup

*To be completed*

---

# 6. SSH Configuration

*To be completed*

---

# 7. Permission Verification Tests

*To be completed*

---

# 8. Log Inspection and Grep

*To be completed*

---

# 9. File Operations

*To be completed*

---

# Troubleshooting

*To be completed*

---

# Conclusion

*To be completed*
