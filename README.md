# Active Directory Home Lab

## Overview

This project is a virtualized enterprise-style Active Directory environment built using **Windows Server 2025** and **VirtualBox**.

The goal of this lab is to gain hands-on experience with:

- Active Directory Domain Services (AD DS)
- DNS
- DHCP
- Group Policy
- Domain joining Windows clients
- Routing and NAT using RRAS
- Windows File Services
- SMB file sharing
- NTFS permissions
- Windows Server administration

This lab simulates a small business network environment with centralized authentication, network services, and file storage.

---

# Network Architecture

![Active Directory Homelab Network Architecture](Images/Active%20Directory%20Homelab%20Network%20Architecture.png)

## Lab Overview

The environment consists of:

| Device | Role | IP Address |
|---|---|---|
| DC01 | Domain Controller, DNS, DHCP | 192.168.10.10 |
| RRAS01 | Router, NAT Gateway | 192.168.10.1 |
| FS01 | File Server | 192.168.10.20 |
| CLIENT01 | Domain Joined Windows 11 Client | DHCP |



