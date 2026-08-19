# Active Directory Home Lab

## Overview

This project is a multi-server Active Directory home lab built in Oracle VirtualBox to simulate a small enterprise Windows environment. The lab consists of a Domain Controller (DC01), routing/NAT server (RRAS01), file server (FS01), and domain-joined Windows 11 client (CLIENT01).

The environment was configured with Active Directory Domain Services, DNS, DHCP, Group Policy, RRAS/NAT, and SMB file sharing. It also includes departmental Organizational Units, security groups, role-based file permissions, automatic network drive mapping, and centralized workstation policies.

The project provided hands-on experience with Windows Server administration, Active Directory, networking, access control, Group Policy, and troubleshooting in a virtualized environment.

---

## Network Architecture

![Active Directory Homelab Network Architecture](Images/Active%20Directory%20Homelab%20Network%20Architecture.png)

## Lab Environment

The lab was built in Oracle VirtualBox using four virtual machines, with each system assigned a dedicated role within the environment.

![VirtualBox Lab Environment](Images/Lab%20Environment.png)

| Machine      | Role                                            |
| ------------ | ----------------------------------------------- |
| **DC01**     | Active Directory Domain Services, DNS, and DHCP |
| **RRAS01**   | Routing, NAT, and internet gateway              |
| **FS01**     | SMB file server and departmental shares         |
| **CLIENT01** | Domain-joined Windows 11 workstation            |

## Network Configuration

The lab uses an **isolated internal network** for communication between domain systems. Static IPv4 addresses were assigned to the servers so that critical services such as Active Directory, DNS, DHCP, file sharing, and routing remain available at consistent addresses.

### DC01

DC01 is connected to the internal network using a **static IPv4 configuration**. It serves as the Domain Controller, DNS server, and DHCP server for the environment.

### RRAS01

RRAS01 uses **two network interfaces** to route traffic between the internal lab network and the external network. The internal interface serves as the gateway for systems within the lab, while the external interface provides connectivity outside of the isolated network.

RRAS01 was configured with **Routing and Remote Access Service (RRAS)** and **Network Address Translation (NAT)** to provide external network access to systems on the internal network.

---

## Active Directory Configuration

**Active Directory Domain Services (AD DS)** was deployed on DC01, which was promoted to a Domain Controller for the `homelab.com` domain. Active Directory provides centralized authentication and management for users, computers, groups, and other domain resources.

### Organizational Unit Structure

Organizational Units (OUs) were created to logically organize resources within the domain. The `Branch1` OU contains dedicated OUs for:

* **Computers**
* **Departments**
* **Servers**
* **Users**

Departmental security groups were created for **Engineering, Marketing, HR, and IT** to simplify access control and Group Policy targeting.

![Active Directory OU Structure](Images/OU%20Overview.png)

---

## Domain Client Configuration

CLIENT01 was configured as a **Windows 11 workstation** and joined to the `homelab.com` Active Directory domain. Domain users can authenticate to the workstation using their Active Directory credentials and receive access to domain-managed resources and policies.

CLIENT01 was used throughout the lab to test DNS resolution, DHCP configuration, internet connectivity, Group Policy application, and departmental file share access.

![CLIENT01 Domain Joined](Images/CLIENT01%20info.png)

---

## DNS & DHCP Configuration

DC01 provides centralized **DNS and DHCP services** for the internal network, allowing clients to automatically receive their network configuration and resolve internal and external hostnames.

### DNS Configuration

DNS was configured on DC01 to provide name resolution for the `homelab.com` domain. A **reverse lookup zone** and PTR records were configured to support IP address-to-hostname resolution.

External DNS forwarders were configured to forward queries that cannot be resolved by the local DNS server.

![DNS Forwarders](Images/DNS%20Forwarder.png)

![DNS Reverse Lookup Zone](Images/DNS%20Reverse%20+%20PTR.png)

Reverse DNS resolution was tested from CLIENT01 to confirm that internal IP addresses correctly resolved to their associated hostnames.

![Reverse DNS Lookup Test](Images/Reverse%20DNS%20Lookup.png)

### DHCP Configuration

The **DHCP Server role** was configured on DC01 to automatically provide network configuration to client devices on the internal network. A DHCP scope was created with an address range and options for the lab environment.

The DHCP scope provides clients with:

* IPv4 address
* Subnet mask
* Default gateway
* DNS server
* DNS domain name

![DHCP Configuration on DC01](Images/DC01%20DHCP%20Config.png)

DHCP functionality was verified from CLIENT01 using `ipconfig /all`. The workstation successfully received its IPv4 configuration from DC01, including the configured default gateway and DNS server.

![DHCP Configuration on CLIENT01](Images/CLIENT01%20DHCP.png)

---

## RRAS & NAT Configuration

RRAS01 was configured with **Routing and Remote Access Service (RRAS)** to provide routing and **Network Address Translation (NAT)** for the internal lab network.

Using its two network interfaces, RRAS01 routes traffic between the isolated Active Directory network and the external network. NAT allows internal systems to access external networks without directly connecting the lab's internal network to the host network.

![RRAS01 Routing and NAT Configuration](Images/RRAS01%20IP%20Config.png)

Connectivity was verified from CLIENT01 by successfully pinging `google.com`, confirming that routing, NAT, and external DNS resolution were functioning correctly.

![CLIENT01 Internet Connectivity Test](Images/CLIENT01%20Ping%20Google.png)

---

## Group Policy Configuration

**Group Policy** was used to centrally manage workstation settings and user policies across the `homelab.com` domain. Multiple Group Policy Objects (GPOs) were created and linked to the appropriate Organizational Units.

![Group Policy Overview](Images/GPO%20Overview.png)

### Workstation Restrictions

A GPO was configured to disable access to the **Windows Control Panel and Settings** for targeted domain users.

![Control Panel Restriction GPO](Images/GPO%20-%20Disable%20Control%20Panel.png)

The policy was validated from CLIENT01 using a standard domain user account, confirming that access to Control Panel was successfully restricted.

![Control Panel Restriction Test GPO](Images/GPO%20-%20Disable%20Control%20Panel%20Test.png)

### Screen Saver Policy

A screen saver timeout policy was configured through Group Policy to automatically apply the configured timeout to targeted domain users.

![Screen Saver Timeout Policy](Images/GPO%20-%20Screen%20Saver.png)

### Windows Update Policy

Windows Update settings were centrally configured through Group Policy to provide consistent update behavior across targeted domain systems.

![Windows Update GPO](Images/GPO%20-%20Auto%20Update.png)

---

## SMB File Shares

Departmental **SMB file shares** were created on FS01 to provide centralized network storage for users within the `homelab.com` domain. Separate shared folders were created for **Engineering, Marketing, HR, and IT**.

The shares are hosted on FS01 and can be accessed using UNC paths such as `\\FS01\Engineering`.

![Departmental File Shares](Images/Driveshare%20Folders.png)

These shares are used with Active Directory security groups and NTFS permissions to control departmental access.

### NTFS Permissions & Access Control

**NTFS permissions** were configured on the departmental folders based on **Active Directory security group membership**. Instead of assigning permissions directly to individual users, departmental security groups were granted access to their corresponding folders.

For example, the **Engineering** security group was granted access to the Engineering folder on FS01, while users outside of the authorized group were restricted from accessing it.

![Engineering NTFS Permissions](Images/Engineering%20Driveshare%20Security.png)

Permissions were validated from CLIENT01 using domain user accounts. An authorized user was able to access the appropriate departmental share, while an unauthorized user was denied access.

![Authorized Share Access](Images/Engineering%20Drive%20Share%20Accessible.png)

![Unauthorized Share Access](Images/Fileshare%20Access%20Denied.png)

---

## Automatic Network Drive Mapping

**Group Policy Preferences** were configured to automatically map departmental network drives when users sign in to a domain-joined workstation. Each mapped drive connects to its corresponding SMB share hosted on FS01.

Drive mappings were targeted using **Active Directory security group membership**, so users receive the network drives associated with their department.

![Automatic Drive Mapping GPO](Images/All%20Drive%20Maps.png)

**Item-level targeting** was configured for each drive mapping to apply it only when the signed-in user is a member of the corresponding departmental security group.

![Drive Mapping Security Group Targeting](Images/Drive%20Map%20Security.png)

The configuration was validated from CLIENT01 by signing in with domain accounts and confirming that the appropriate departmental drives were automatically mapped.

![Mapped Network Drives](Images/Drive%20Map%20Result.png)

---

## Testing & Troubleshooting

The environment was tested throughout deployment to verify network connectivity, name resolution, authentication, Group Policy application, and access to network resources.

### Network Connectivity Troubleshooting

During testing, **Windows Firewall** initially prevented communication between systems on the internal network. Commands such as `ping` and `ipconfig` were used to verify addressing and connectivity while isolating the issue.

After correcting the firewall configuration, communication between the affected systems was restored.

![Firewall Connectivity Issue](Images/Firewall%20Issue.png)

Testing performed throughout the lab included:

* DHCP address assignment
* Forward and reverse DNS resolution
* External connectivity through RRAS/NAT
* Domain user authentication
* Group Policy application
* SMB file share access
* NTFS permission enforcement
* Automatic network drive mapping

---

## Technologies & Skills

### Technologies Used

* Windows Server 2025
* Windows 11
* Active Directory Domain Services (AD DS)
* Group Policy
* DNS & DHCP
* Routing and Remote Access Service (RRAS)
* Network Address Translation (NAT)
* SMB File Sharing
* NTFS Permissions
* IPv4 Networking
* Oracle VirtualBox

### Skills Demonstrated

* Active Directory administration and domain management
* User, group, and Organizational Unit management
* DNS and DHCP configuration
* IPv4 addressing, routing, and network troubleshooting
* Group Policy creation and deployment
* Role-based access control using security groups
* SMB file server administration
* NTFS permission management
* Automated network drive deployment
* Windows client administration and domain integration







