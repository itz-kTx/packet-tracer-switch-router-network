# Packet Tracer Lab: Build a Switch and Router Network

## 1. Objective

The objective of this lab was to build and configure a basic routed network in Cisco Packet Tracer. The lab used two PCs, one switch, and one router to demonstrate basic network connectivity between two different IPv4 networks.

The lab focused on:

- Connecting devices using copper straight-through cables
- Configuring IPv4 addresses and default gateways
- Configuring router interfaces
- Configuring a switch management interface
- Testing connectivity with `ping`
- Enabling basic device security settings
- Generating RSA keys for SSH access

---

## 2. Initial Topology

The lab started with four unconnected devices:

- PCA
- S1 switch
- R1 router
- PCB

At this point, no devices were connected and no IP addressing or routing configuration had been completed.

![Initial Setup](images/initial-set-up.png)

---

## 3. Network Topology

The final topology connected the devices as follows:


PCA ---- S1 ---- R1 ---- PCB

The topology used two separate networks:

Network                    Devices
- 192.168.1.0/24        PCA,S1,VLAN 1, R1, G0/0/1
- 192.168.0.0/24        PVB,R1,G0/0/0


## 4.Addressing table

Device	Interface	  IP Address	  Subnet Mask	   Default Gateway
R1	    G0/0/0	    192.168.0.1	  255.255.255.0	  N/A
R1	    G0/0/1	    192.168.1.1	  255.255.255.0  	N/A
S1	    VLAN 1	    192.168.1.2	  255.255.255.0	  192.168.1.1
PCA	    NIC	        192.168.1.3	  255.255.255.0	  192.168.1.1
PCB	    NIC	        192.168.0.3	  255.255.255.0	  192.168.0.1

## 5.Initial Connectivity Test

After connecting the devices, I attempted to ping PCB from PCA using:
- ping 192.168.0.3

The ping failed because the router had not been configured yet. Without configured and enabled router interfaces, traffic could not be routed between the 192.168.1.0/24 and 192.168.0.0/24 networks.

This showed that physical connections alone are not enough. The router must have valid IP addresses on its interfaces, and those interfaces must be enabled.


## 6.Router Configuration

R1 was configured with basic security settings and IP addresses on both router interfaces.

The router configuration included:
enable
configure terminal

hostname R1
enable secret class

line console 0
password cisco
login
exit

service password-encryption
banner motd "Unauthorized access is prohibited."

interface gigabitEthernet0/0/0
ip address 192.168.0.1 255.255.255.0
no shutdown
exit

interface gigabitEthernet0/0/1
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

end
copy running-config startup-config

The no shutdown command was required because router interfaces are disabled by default.


## 7.Swtich Configuration

S1 was configured with a hostname, console password, encrypted passwords, a banner message, a VLAN 1 management IP address, and a default gateway.

The switch configuration included:
enable
configure terminal

hostname S1
enable secret class

line console 0
password cisco
login
exit

service password-encryption
banner motd "Unauthorized access is prohibited."

interface vlan 1
ip address 192.168.1.2 255.255.255.0
no shutdown
exit

ip default-gateway 192.168.1.1

end
copy running-config startup-config

The switch default gateway was set to 192.168.1.1, which is the R1 interface on the same network as the switch management VLAN.

## 8.Succesfull Connectivity Test

After configuring the router, switch, and PC addressing, I tested connectivity again from PCA to PCB:
- ping 192.168.0.3

  This time, the ping was successful.

The first ping attempt lost packets, but the second attempt succeeded. This can happen because ARP needs to resolve MAC addresses before communication fully succeeds.

The successful ping confirmed that traffic could route between the two networks through R1.

## 9. RSA Key Generation and SSH Preparation

After basic connectivity was working, RSA keys were generated on R1 to prepare the router for SSH access.

The following commands were used:
-configure terminal
-ip domain-name academy.net
-crypto key generate rsa

When prompted for the key modulus size, 1024 bits was selected:
- How many bits in the modulus [512]: 1024

A local user account was then created:
- username SSHuser secret cisco

The VTY lines were configured to use local login and allow SSH:
- line vty 0 4
- login local
- transport input ssh

This step is security-relevant because SSH provides encrypted remote management access, unlike Telnet, which sends traffic in plaintext.

## 10.Troubleshooting Notes

During the lab, several issues were encountered and fixed.

Issue	                            Cause	                                                                   Fix
PCA could not ping PCB at first	                Router interfaces were not configured	                    Configured R1 interfaces with IP addresses
Router interfaces were down	                    Interfaces were administratively disabled	                Used no shutdown
Switch management was not available initially	  VLAN 1 was not configured	                                Assigned IP address to VLAN 1
Default gateway configuration caused confusion	Command had to be entered in global configuration mode	  Used configure terminal before entering the command
First successful ping lost packets	            ARP resolution	                                          Re-ran the ping and confirmed connectivity
SSH configuration required RSA keys	            SSH needs a domain name and RSA keys	                     Configured domain name and generated 1024-bit RSA keys


## 11.Security Relevance

This lab is relevant to cybersecurity because it demonstrates core networking concepts needed for security operations, incident response, and cloud security.

The lab reinforced the importance of:

- Understanding subnets and default gateways
- Knowing how traffic moves between networks
- Recognizing the role of routers and switches
- Configuring secure device access
- Using SSH instead of Telnet
- Troubleshooting failed connectivity
- Verifying configuration changes with practical tests

For SOC analysis, cloud security, and network security, understanding routing and switching is a foundation skill. Without knowing how traffic should move, it is much harder to investigate suspicious or broken network behavior.


## 12.Lesson Learned

This lab showed that a network can be physically connected but still fail if devices are not configured correctly.

The most important lessons were:
- Routers need IP addresses on their interfaces to route traffic.
- Router interfaces must be enabled with no shutdown.
- PCs need the correct default gateway to communicate outside their local network.
- Switches can be assigned a management IP through VLAN 1.
- SSH requires a domain name, RSA keys, and local login configuration.
- Successful ping tests are useful evidence that connectivity is working.
- Small CLI mistakes can break configuration, so command modes matter.


## 13.Conclusion


This Packet Tracer lab successfully demonstrated how to build and configure a basic routed network. After the router, switch, and PCs were configured, PCA was able to communicate with PCB across two different networks.

The lab also introduced basic device security configuration, including password encryption, login protection, banners, and RSA key generation for SSH access.

Overall, this lab helped strengthen networking fundamentals that are important for future cybersecurity work, especially in SOC analysis, network security, and cloud security.
   
