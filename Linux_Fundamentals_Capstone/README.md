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

## Initial Environment Assessment

Before creating users and groups, the system was inspected.

### Existing Groups

```bash
getent group engineers
getent group sales
getent group hr
```

### Findings

The required groups already existed in the lab environment and had users assigned from a previous session.

### Existing Users

```bash
id alice
id bob
id carol
id dave
id eve
```

### Findings

- alice already existed
- bob already existed
- carol already existed
- dave did not exist
- eve did not exist

The existing configuration did not match the project requirements.

---

## Environment Cleanup

To ensure the project matched the capstone requirements, the existing users and groups were removed and recreated from scratch.

### Commands

```bash
sudo userdel -r alice
sudo userdel -r bob
sudo userdel -r carol

sudo groupdel engineers
sudo groupdel sales
sudo groupdel hr
```

### Result

The environment was reset to a clean state before continuing with the official project setup.

---

## Creating Department Groups

Created groups for the three departments required by the project.

### Commands

```bash
sudo groupadd engineers
sudo groupadd sales
sudo groupadd hr
```

### Verification

```bash
getent group engineers
getent group sales
getent group hr
```

Output:

```text
engineers:x:1018:
sales:x:1019:
hr:x:1020:
```

### Result

Successfully created:

- engineers
- sales
- hr

---

## Creating Users

Created five user accounts with home directories.

### Commands

```bash
sudo useradd -m alice
sudo useradd -m bob
sudo useradd -m carol
sudo useradd -m dave
sudo useradd -m eve
```

### Verification

```bash
id alice
id bob
id carol
id dave
id eve
```

Output:

```text
uid=1015(alice) gid=1021(alice) groups=1021(alice)
uid=1016(bob) gid=1022(bob) groups=1022(bob)
uid=1017(carol) gid=1023(carol) groups=1023(carol)
uid=1018(dave) gid=1024(dave) groups=1024(dave)
uid=1019(eve) gid=1025(eve) groups=1025(eve)
```

### Password Configuration

Passwords were configured for all user accounts using:

```bash
sudo passwd alice
sudo passwd bob
sudo passwd carol
sudo passwd dave
sudo passwd eve
```

---

## Assigning Users to Department Groups

### Commands

```bash
sudo usermod -aG engineers alice
sudo usermod -aG engineers bob

sudo usermod -aG sales carol
sudo usermod -aG sales dave

sudo usermod -aG hr eve
```

### Purpose

Assigned users to their respective department groups to enable group-based permission management.

### Verification

```bash
groups alice
groups bob
groups carol
groups dave
groups eve
```

Output:

```text
alice : alice engineers
bob : bob engineers
carol : carol sales
dave : dave sales
eve : eve hr
```

### Result

Users were successfully assigned to their respective department groups:

| User | Department Group |
|------|------------------|
| alice | engineers |
| bob | engineers |
| carol | sales |
| dave | sales |
| eve | hr |
```

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
