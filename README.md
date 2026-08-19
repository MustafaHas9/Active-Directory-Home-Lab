# Active Directory Home Lab

## Overview

This project is a multi-server Active Directory home lab built in Oracle VirtualBox to simulate a small enterprise Windows environment. The lab consists of a Domain Controller (DC01), routing/NAT server (RRAS01), file server (FS01), and domain-joined Windows 11 client (CLIENT01).

The environment was configured with Active Directory Domain Services, DNS, DHCP, Group Policy, RRAS/NAT, and SMB file sharing. It also implements departmental Organizational Units, security groups, role-based file permissions, automatic network drive mapping, and centralized workstation policies.

The project demonstrates hands-on experience with Windows Server administration, Active Directory, networking, access control, Group Policy, and troubleshooting in a virtualized enterprise-style environment.


## Network Architecture

![Active Directory Homelab Network Architecture](Images/Active%20Directory%20Homelab%20Network%20Architecture.png)

## Network Configuration

The lab uses an **isolated internal network** for communication inside the domain. Static IPv4 addresses were assigned to the servers to ensure that services such as Active Directory, DNS, DHCP, and routing remain consistently available.

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

The **DHCP Server role** was configured on DC01 to automatically provide network configuration to client devices on the internal network. A DHCP scope was created with the appropriate address range and options for the lab environment.

The DHCP scope provides clients with:

* IPv4 address
* Subnet mask
* Default gateway
* DNS server
* DNS domain name

![DHCP Configuration on DC01](Images/DC01%20DHCP%20Config.png)

DHCP functionality was verified from CLIENT01 using `ipconfig /all`. The workstation successfully received its IPv4 configuration from DC01, including the configured default gateway and DNS server.

![DHCP Configuration on CLIENT01](Images/CLIENT01%20DHCP.png)

## RRAS & NAT Configuration

RRAS01 was configured with **Routing and Remote Access Service (RRAS)** to provide routing and Network Address Translation (NAT) for the internal lab network.

Using its two network interfaces, **RRAS01** routes traffic between the isolated Active Directory network and the external network. **NAT** allows internal systems to access external networks while keeping the lab environment separated from the host network.

![RRAS01 Routing and NAT Configuration](Images/RRAS01%20IP%20Config.png)

Connectivity was verified from **CLIENT01** by successfully pinging google.com, confirming that **routing, NAT, and DNS resolution** were functioning correctly.

![CLIENT01 Internet Connectivity Test](Images/CLIENT01%20Ping%20Google.png)

## Group Policy Configuration

**Group Policy** was used to centrally manage security settings, workstation configurations, and user policies across the `homelab.com` domain. Multiple Group Policy Objects (GPOs) were created and linked to the appropriate Organizational Units to apply settings based on users and computers within the environment.

![Group Policy Overview](Images/GPO%20Overview.png)

### Workstation Restrictions

A Group Policy Object was configured to **disable access to the Windows Control Panel and Settings** for targeted domain users. This demonstrates how administrative restrictions can be centrally enforced across domain-joined workstations.

![Control Panel Restriction GPO](Images/GPO%20-%20Disable%20Control%20Panel.png)




Attempting to access control panel from a non-admin account causes this error message.

![Control Panel Restriction Test GPO](Images/GPO%20-%20Disable%20Control%20Panel%20Test.png)

### Screen Saver Policy

A screen saver timeout policy was configured to automatically apply workstation security settings to domain users. This provides centralized control over workstation behavior without requiring configuration on each individual client.

![Screen Saver Timeout Policy](Images/GPO%20-%20Screen%20Saver.png)

### Windows Update Policy

Windows Update settings were centrally configured through Group Policy to provide consistent update behavior across domain-joined systems.

![Windows Update GPO](Images/GPO%20-%20Auto%20Update.png)


## SMB File Shares

Departmental **SMB file shares** were created on FS01 to provide centralized network storage for users within the `homelab.com` domain. Separate shared folders were created for **Engineering, Marketing, HR, and IT**, allowing each department to maintain its own network-accessible storage.

The shares are hosted centrally on FS01 and can be accessed by domain users using paths such as `\\FS01\Engineering`, for the **Engineering** folder for example.

![Departmental File Shares](Images/Driveshare%20Folders.png)

The shared folders provide the network resources later used for **group-based access control and automatic drive mapping through Group Policy**.

## NTFS Permissions & Access Control

**NTFS permissions** were configured on the departmental folders to control access based on **Active Directory security group membership**. Instead of assigning permissions directly to individual users, departmental security groups were granted access to their corresponding folders, allowing permissions to be managed centrally through Active Directory.

For example, the **Engineering** security group was granted access to the Engineering folder on FS01, while users outside of the authorized group were restricted from accessing the resource.

![Engineering NTFS Permissions](Images/Engineering%20Driveshare%20Security.png)

Permissions were validated from CLIENT01 using domain user accounts. An authorized user was able to access the appropriate departmental share.

![Authorized Share Access](Images/Engineering%20Drive%20Share%20Accessible.png)

While an unauthorized user was denied access.

![Unauthorized Share Access](Images/Fileshare%20Access%20Denied.png)

This configuration demonstrates **role-based access control using Active Directory security groups and NTFS permissions**, allowing access to departmental resources to be managed without configuring permissions individually for each user.


## Automatic Network Drive Mapping

**Group Policy Preferences** were configured to automatically map departmental network drives for domain users when they sign in to CLIENT01. Each mapped drive points to the appropriate SMB share hosted on FS01.

Drive mapping is targeted using **Active Directory security group membership**, ensuring users automatically receive access only to the network drives associated with their department.

![Automatic Drive Mapping GPO](Images/All%20Drive%20Maps.png)

Security-group targeting was configured for each departmental drive so that the appropriate mapping is applied based on the authenticated user's group membership.

![Drive Mapping Security Group Targeting](Images/Drive%20Map%20Security.png)

The configuration was validated from CLIENT01 by signing in with domain accounts and confirming that the appropriate network drives were automatically mapped.

![Mapped Network Drives](Images/Drive%20Map%20Result.png)

## Testing & Troubleshooting

Throughout the deployment, the environment was tested from both server and client systems to verify **network connectivity, name resolution, authentication, Group Policy application, and resource access**.

### Network Connectivity Troubleshooting

During testing, Windows Firewall initially prevented network communication between systems on the internal network. Connectivity was tested using tools such as `ping` and `ipconfig` to isolate the issue and verify the network configuration.

After correcting the firewall configuration, communication between the systems was restored and connectivity across the internal network was successfully verified.

![Firewall Connectivity Issue](Images/Firewall%20Issue.png)


Additional validation performed throughout the lab included:

* DHCP address assignment
* Forward and reverse DNS resolution
* External network connectivity through RRAS/NAT
* Domain user authentication
* Group Policy application
* SMB file share access
* NTFS permission enforcement
* Automatic network drive mapping

## Technologies & Skills Demonstrated

This project provided hands-on experience deploying and administering a **multi-server Windows domain environment** and integrating core enterprise infrastructure services.

**Technologies Used:**

* Windows Server 2025
* Windows 11
* Active Directory Domain Services (AD DS)
* Group Policy
* DNS & DHCP
* Routing and Remote Access Service (RRAS)
* NAT and Internal Networks (LAN)
* SMB File Sharing
* NTFS Permissions
* VirtualBox

**Skills Demonstrated:**

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







