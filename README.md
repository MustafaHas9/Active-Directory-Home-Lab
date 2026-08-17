# Active Directory Home Lab

## Overview

This project is a multi-server Active Directory home lab built in Oracle VirtualBox to simulate a small enterprise Windows environment. The lab consists of a Domain Controller (DC01), routing/NAT server (RRAS01), file server (FS01), and domain-joined Windows 11 client (CLIENT01).

The environment was configured with Active Directory Domain Services, DNS, DHCP, Group Policy, RRAS/NAT, and SMB file sharing. It also implements departmental Organizational Units, security groups, role-based file permissions, automatic network drive mapping, and centralized workstation policies.

The project demonstrates hands-on experience with Windows Server administration, Active Directory, networking, access control, Group Policy, and troubleshooting in a virtualized enterprise-style environment.

--

## Network Architecture

![Active Directory Homelab Network Architecture](Images/Active%20Directory%20Homelab%20Network%20Architecture.png)


## Lab Environment

The lab was built in Oracle VirtualBox using four virtual machines, with each system assigned a dedicated role within the environment.


![VirtualBox Lab Environment](Images/Lab%20Environment.png)

| Machine | Role |
|---|---|
| **DC01** | Active Directory Domain Services, DNS, and DHCP |
| **RRAS01** | Routing, NAT, and internet gateway |
| **FS01** | SMB file server and departmental shares |
| **CLIENT01** | Domain-joined Windows 11 workstation |


