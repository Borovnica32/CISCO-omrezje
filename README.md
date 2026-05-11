# Network Infrastructure Project

This project demonstrates how to design, implement, and maintain a structured network using Windows Server, RADIUS, Active Directory (AD), Network Policy Server (NPS), Domain Controller (DC) in adition with TrueNas Storage server and advanced configurations of Cisco devices (Routers and Switches) and MikroTik HAP routers used as Access Points (AP) for WI-FI

# Overview

The networ consists of two Cisco 2900 routers witch are connected to each other wia a main ethernet line and a redundent line also, on each router there is one Cisco switch (Catalyst 2960 switch). On one end of the network there is a Windows Server and a MikroTik AP (look at the topology) and in the other end there is a TrueNAS Storage server.


on the Windows Server there is a running AD witchcurrently contains two groups: netWatch (network administrators) and regular users. RADIUS and NPS are used together to authenticate and authorize members of the netWatch group when accessing Cisco devices. Access is restricted so that netWatch users can connect via SSH only from designated management network segments (see network topology) they connect with their AD username and their password.



# Authentication and Authorization

Each Cisco device is registered on the RADIUS server with a pre-shared key. The devices are configured to communicate with the RADIUS server using AAA (authentication, authorization, and accounting), specifically with commands such as "aaa authentication", "aaa authorization", and "aaa group server radius".



Each device also has a fallback local account in case the RADIUS server becomes unavailable, if this happenes the user has to have physical access to the devices and has to connect using a console cable.



SSH access is restricted using access control lists. Only users from management network segments can connect, and only to specific IP addresses on each device (loopback interfaces for routers or management IPs for switches).



# Network Design

Each router has a loopback interface and multiple VLANs configured. OSPF is used for routing between networks, and there is a redundant connection between the two routers, for security vlan interfaces have been set up as passive-interfaces to prevent rouge routers from being connected and astablishing a neighbouring connection.



As mentioned before switches do not use loopback interfaces. Instead, they use the second IP address from VLAN 100, which is designated as the management VLAN. Trunk ports are configured on switches to allow VLAN traffic between different network segments.



Currently, the network includes three VLANs: VLAN 10, VLAN 20 (R1 Only), and VLAN 100.
  - VLAN 10 is used for connecting regular devices (PC, Laptops etc.)
  - VLAN 20 (R1 only) is used for WI-FI conectivity (Mobile devices)
  - VLAN 100 is used by managment, all administrative users are in this segment (IT)


![Network Topology](./Topologija/Diagram%20Cisco%20omprezja%20IPv4%20Public.png)




# Wireless and DHCP

For WI-FI access the main router (R1) has a configured DHCP pool used for the Wi-Fi network. For actual WI-FI a MikroTik access points (one connected to S1) provide wireless access and is a s all other devices registered with RADIUS, for security the WI-FI requieres login with AD credentials password and username.



Guests can connect using a password and do not have access to internal network services. User authentication via Active Directory for Wi-Fi access and have access to the internal network and services.



# Additional Services
Foe aditional services as mentionde before are TrueNAS witch is running TFTP server for configuration backups withc are automaticly savet to he TFTP server when "write memory" is ran on a switch or router (see config files), SMB for sharing these backups (only fmanagement personel) and SSH access for management personel. On Windows Server there is a aditional service running witch is the Internet Information Service (IIS) witch hosts a website where a copy of this documentation is displayed
