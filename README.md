# Active Directory Home Lab

## Overview

This project is a multi-server Active Directory home lab built in Oracle VirtualBox to simulate a small enterprise Windows environment. The lab consists of a Domain Controller (DC01), routing/NAT server (RRAS01), file server (FS01), and domain-joined Windows 11 client (CLIENT01).

The environment was configured with Active Directory Domain Services, DNS, DHCP, Group Policy, RRAS/NAT, and SMB file sharing. It also implements departmental Organizational Units, security groups, role-based file permissions, automatic network drive mapping, and centralized workstation policies.

The project demonstrates hands-on experience with Windows Server administration, Active Directory, networking, access control, Group Policy, and troubleshooting in a virtualized enterprise-style environment.

--

## Network Architecture

![Active Directory Homelab Network Architecture](Images/Active%20Directory%20Homelab%20Network%20Architecture.png)

## Network Configuration

The lab uses an **isolated internal network** for communication between the domain infrastructure. Static IPv4 addresses were assigned to the servers to ensure that services such as Active Directory, DNS, DHCP, and routing remain consistently available.

### DC01

DC01 is connected to the internal network and uses a **static IPv4 configuration**. It serves as the primary **Domain Controller, DNS server, and DHCP server** for the environment.


### RRAS01

RRAS01 uses **two network interfaces** to route traffic between the internal lab network and the external network. The internal interface provides a gateway for domain systems, while the external interface provides connectivity outside the isolated network.

RRAS01 is later configured with **Routing and Remote Access Service (RRAS) and Network Address Translation (NAT)** to allow internal systems to access the internet.



## Lab Environment

The lab was built in Oracle VirtualBox using four virtual machines, with each system assigned a dedicated role within the environment.


![VirtualBox Lab Environment](Images/Lab%20Environment.png)

| Machine | Role |
|---|---|
| **DC01** | Active Directory Domain Services, DNS, and DHCP |
| **RRAS01** | Routing, NAT, and internet gateway |
| **FS01** | SMB file server and departmental shares |
| **CLIENT01** | Domain-joined Windows 11 workstation |

## Active Directory Configuration

**Active Directory Domain Services (AD DS)** was deployed on DC01, which was promoted to a Domain Controller for the `homelab.com` domain. Active Directory provides centralized authentication and management for users, computers, groups, and other resources throughout the lab.

### Organizational Unit Structure

Organizational Units (OUs) were created to simulate the structure of a real organization and provide logical separation between users, computers, and departments.

The `Branch1` OU contains dedicated OUs for:


* **Computers**
* **Departments**
* **Servers**
* **Users**


Security groups: **Engineering, Marketing, HR and IT** were organized within their respective OUs. This structure allows **Group Policy and access controls** to be applied to specific users and departments rather than across the entire domain.

![Active Directory OU Structure](Images/OU%20Overview.png)

## Domain Client Configuration

CLIENT01 was configured as a **Windows 11 workstation** and joined to the `homelab.com` Active Directory domain. This allows domain users to authenticate using centralized Active Directory credentials and provides the workstation access to domain-managed resources and policies.

CLIENT01 is also used throughout the lab to validate **DNS resolution, DHCP configuration, internet connectivity, Group Policy application, and access to departmental file shares**.

![CLIENT01 Domain Joined](Images/CLIENT01%20info.png)


## DNS & DHCP Configuration

DC01 provides centralized **DNS and DHCP services** for the internal network, allowing domain systems to automatically receive network configurations and resolve both internal and external hostnames.

### DNS Configuration

DNS was configured alongside Active Directory to provide name resolution for the `homelab.com` domain. A **reverse lookup zone** and PTR records were configured to support IP-to-hostname resolution.

External DNS forwarders were also configured, allowing DNS requests that cannot be resolved locally to be forwarded to external DNS servers.

![DNS Forwarders](Images/DNS%20Forwarder.png)


![DNS Reverse Lookup Zone](Images/DNS%20Reverse%20+%20PTR.png)

Reverse DNS resolution was later tested with domain-joined **CLIENT01** to verify that IP addresses correctly resolve to their associated hostnames.

![Reverse DNS Lookup Test](Images/Reverse%20DNS%20Lookup.png)

### DHCP Configuration

A DHCP scope was configured on DC01 to automatically provide network configuration to domain clients. The scope distributes settings including:

* IPv4 addresses




