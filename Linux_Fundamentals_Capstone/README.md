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

Created department shared directories under `/srv/company`.

## Commands

```bash
sudo mkdir -p /srv/company/{engineering,sales,hr}
```

## Group Ownership

```bash
sudo chgrp engineers /srv/company/engineering
sudo chgrp sales /srv/company/sales
sudo chgrp hr /srv/company/hr
```

## Verification

```bash
tree /srv/company
```

Output:

```text
/srv/company
├── engineering
├── hr
└── sales
```

### Notes

During verification, additional directories and files from previous lab exercises were found and removed:

- /srv/company/shared
- /srv/company/engineering/server_info.txt
- /srv/company/engineering/test_alice.txt


---

# 4. Permissions Configuration

Configured department folders and private user directories.

## Shared Department Folders

Commands:

```bash
sudo chmod 775 /srv/company/engineering
sudo chmod 775 /srv/company/sales
sudo chmod 775 /srv/company/hr
```

Verification:

```bash
ls -ld /srv/company/*
```

Output:

```text
drwxrwxr-x engineering
drwxrwxr-x hr
drwxrwxr-x sales
```

Meaning:

- Owner: Read, Write, Execute
- Group: Read, Write, Execute
- Others: Read, Execute

## Private User Directories

Commands:

```bash
sudo -u alice mkdir /home/alice/private
sudo -u bob mkdir /home/bob/private
sudo -u carol mkdir /home/carol/private
sudo -u dave mkdir /home/dave/private
sudo -u eve mkdir /home/eve/private
```

```bash
sudo chmod 700 /home/alice/private
sudo chmod 700 /home/bob/private
sudo chmod 700 /home/carol/private
sudo chmod 700 /home/dave/private
sudo chmod 700 /home/eve/private
```

Verification:

```bash
ls -ld /home/alice/private
ls -ld /home/bob/private
ls -ld /home/carol/private
ls -ld /home/dave/private
ls -ld /home/eve/private
```

Output:

```text
drwx------ /home/alice/private
drwx------ /home/bob/private
drwx------ /home/carol/private
drwx------ /home/dave/private
drwx------ /home/eve/private
```

Meaning:

- Owner: Full access
- Group: No access
- Others: No access

---

# 5. Nginx Setup

## Service Verification

Checked the status of the Nginx service.

### Command

```bash
sudo systemctl status nginx
```

### Result

The Nginx service was running successfully.

Status:

```text
Active: active (running)
```

---

## Service Control Testing

### Stop Service

```bash
sudo systemctl stop nginx
```

Verification:

```bash
sudo systemctl status nginx
```

Result:

```text
Active: inactive (dead)
```

This confirmed that the service stopped successfully.

---

### Start Service

```bash
sudo systemctl start nginx
```

Verification:

```bash
sudo systemctl status nginx
```

Result:

```text
Active: active (running)
```

This confirmed that the service started successfully.

---

### Restart Service

```bash
sudo systemctl restart nginx
```

Verification:

```bash
sudo systemctl status nginx
```

Result:

```text
Active: active (running)
```

The service restarted successfully.

---

## Skills Demonstrated

- Service status inspection
- Service start/stop operations
- Service restart operations
- Systemd service management

---

# 6. SSH Access Testing

## SSH Service Verification

Verified that the SSH service was running.

### Command

```bash
sudo systemctl status ssh
```

### Result

```text
Active: active (running)
```

---

## SSH Login Test as Alice

### Command

```bash
ssh alice@localhost
```

### User Verification

```bash
whoami
```

Output:

```text
alice
```

### Group Verification

```bash
groups
```

Output:

```text
alice engineers
```

---

## Department Directory Access Test

Verified that Alice could access the engineering department directory.

```bash
ls -ld /srv/company/engineering
```

Output:

```text
drwxrwxr-x 2 root engineers ...
```

---

## File Creation Test

Created a test file within the engineering department directory.

```bash
touch /srv/company/engineering/test_alice.txt
```

Verification:

```bash
ls -l /srv/company/engineering
```

Output:

```text
-rw-rw-r-- 1 alice alice 0 Aug 24 04:00 test_alice.txt
```

### Result

Alice successfully authenticated through SSH and created a file within her department's shared directory.

---

# 7. Access Control Testing

## Cross-Department Access Test

Verified that users cannot modify resources belonging to other departments.

### Login as Carol

```bash
ssh carol@localhost
```

### User Verification

```bash
whoami
```

Output:

```text
carol
```

### Group Verification

```bash
groups
```

Output:

```text
carol sales
```

### Unauthorized Access Attempt

```bash
touch /srv/company/engineering/carol_test.txt
```

Output:

```text
touch: cannot touch '/srv/company/engineering/carol_test.txt': Permission denied
```

### Result

The Linux permission system successfully prevented a Sales department user from creating files inside the Engineering department directory.

This demonstrates proper implementation of group-based access control.

---

# 8. System Information Report

Generated a server information report.

## Commands

```bash
hostname
uname -a
free -h
```

## Purpose

Collected system details for administrative reporting and troubleshooting.
```

---

# 9. Log Monitoring

Inspected service logs using journalctl.

## Commands

```bash
sudo journalctl -u nginx --no-pager -n 10
sudo journalctl -u ssh --no-pager -n 10
```

## Purpose

Reviewed service activity and verified successful operations through system logs.
```

---

# 10. Final Verification

| Requirement | Status |
|------------|---------|
| Ubuntu Server Installed | ✅ |
| Nginx Installed | ✅ |
| OpenSSH Installed | ✅ |
| Git Installed | ✅ |
| Department Groups Created | ✅ |
| Users Created | ✅ |
| User Group Assignment Completed | ✅ |
| Shared Directories Created | ✅ |
| Permissions Configured | ✅ |
| Private Directories Created | ✅ |
| Nginx Managed with systemctl | ✅ |
| SSH Login Tested | ✅ |
| Access Control Verified | ✅ |
| Log Inspection Completed | ✅ |
```

---

# Lessons Learned

Through this project I learned how to:

- Create and manage Linux users and groups
- Configure group-based permissions
- Create secure private directories
- Manage services using systemctl
- Configure and verify SSH access
- Apply access control principles
- Inspect system logs using journalctl
- Document Linux administration tasks professionally
- Use Git and GitHub for project tracking
```

---

# Future Improvements

Possible enhancements include:

- Implementing SSH key authentication
- Configuring a custom Nginx website
- Automating setup with Bash scripts
- Using Ansible for configuration management
- Deploying the environment in AWS EC2
- Adding monitoring with Prometheus and Grafana
```
