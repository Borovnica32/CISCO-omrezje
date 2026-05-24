# Network Infrastructure Project

This project demonstrates the design, implementation, and maintenance of a structured enterprise-style network environment using multiple technologies and platforms. The infrastructure integrates Windows Server services, Cisco networking equipment, MikroTik wireless access points, a TrueNAS storage server, and a Fedora Server running the Elastic Stack for centralized logging and monitoring.

The environment includes:

* Windows Server with Active Directory (AD), RADIUS, Network Policy Server (NPS), and Domain Controller (DC) services
* Cisco routers and switches with advanced configurations
* MikroTik hAP routers configured as wireless access points
* TrueNAS storage for centralized backups and file sharing
* Fedora Server integrated with Active Directory and running Elastic Stack



# Overview

The network consists of two Cisco 2900 series routers connected through both a primary Ethernet link and a redundant backup link to ensure high availability. Each router is connected to a Cisco Catalyst 2960 switch.

On one side of the network, a Windows Server and a MikroTik access point are connected, while the opposite side hosts the TrueNAS storage server. A Fedora Server is also integrated into the infrastructure for centralized log collection and analysis.

The Windows Server hosts an Active Directory environment that currently contains two security groups:

* **netWatch** – network administrators
* **Regular Users** – standard domain users

RADIUS and NPS are integrated with Active Directory to authenticate and authorize members of the **netWatch** group when accessing Cisco network devices. Administrative access is restricted so that authorized users can connect only through designated management network segments using their Active Directory credentials over SSH.

The Fedora Server is joined to the Active Directory domain, allowing members of the **netWatch** group to receive administrative (root/sudo) privileges. The server currently runs the Elastic Stack for centralized logging and monitoring.



# Authentication and Authorization

Each Cisco device is registered on the RADIUS server using a pre-shared key. Devices communicate with the RADIUS server through Cisco AAA (Authentication, Authorization, and Accounting) services using configurations such as:

* `aaa authentication`
* `aaa authorization`
* `aaa accounting`
* `aaa group server radius`

To ensure reliability, every device also contains a fallback local administrator account. If the RADIUS server becomes unavailable, administrators can still access devices through physical console connections using a console cable.

SSH access is additionally protected with Access Control Lists (ACLs). Only hosts within authorized management network segments can establish SSH sessions, and access is limited to specific management IP addresses such as router loopback interfaces or switch management VLAN addresses.



# Network Design

Each router contains:

* Multiple VLAN interfaces
* A dedicated loopback interface for management
* OSPF dynamic routing configuration

OSPF is used to provide routing between all network segments. A redundant connection between routers ensures continued connectivity in the event of a link failure.

For additional security, VLAN interfaces participating in OSPF are configured as **passive interfaces**, preventing unauthorized or rogue devices from forming OSPF neighbor relationships.

Switches do not use loopback interfaces. Instead, management access is provided through the second IP address within **VLAN 100**, which serves as the dedicated management VLAN.

Trunk ports are configured between switches and routers to transport VLAN traffic across the infrastructure.

The network currently contains three VLANs:

* **VLAN 10** – General user devices (PCs, laptops, workstations)
* **VLAN 20** *(R1 only)* – Wireless/Wi-Fi client devices
* **VLAN 100** – Management network for IT administrators

![Network Topology](./Topologija/Diagram%20Cisco%20omprezja%20IPv4%20Public.png)



# Wireless Networking and DHCP

The primary router (R1) provides DHCP services for the wireless network. Wireless connectivity is delivered through MikroTik access points connected to switch S1.

The wireless infrastructure integrates with RADIUS and Active Directory authentication, requiring users to authenticate using their AD username and password before gaining access to internal services.

Two wireless access modes are available:

* **Guest Access** – password-based access with restricted network permissions
* **Authenticated User Access** – Active Directory authentication with access to internal network resources and services

This setup provides secure centralized authentication while separating guest traffic from internal enterprise resources.



# Additional Services

## TrueNAS Server

The TrueNAS server provides several infrastructure services, including:

* **TFTP server** for automated Cisco configuration backups
* **SMB file sharing** for backup storage access
* **SSH management access** for administrators

Cisco devices are configured to automatically save configuration backups to the TFTP server whenever the `write memory` command is executed.

Access to backup files is restricted to authorized management personnel.



## Windows Server Services

In addition to Active Directory and RADIUS services, the Windows Server also hosts:

* **Internet Information Services (IIS)**

The IIS web server hosts a local website containing a copy of this project documentation.



## Fedora Server and Elastic Stack

The Fedora Server is integrated into the Active Directory domain and provides centralized monitoring and log analysis using the Elastic Stack.

The Elastic Stack is used for:

* Centralized log collection
* Device monitoring
* Event analysis


# This project is completly offline so no data can leak from it and that is why passwords can be seen in the uploaded files (basicly noone except me can acces this project)
* Network troubleshooting
* Security monitoring

Logs from network devices and servers are forwarded to the Elastic Stack environment for analysis and visualization.
