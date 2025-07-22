# Active-Directory-Lab-In-VirtualBox

## Objective
The goal of this lab is to build, configure and exploit an Active Directory environment to simulate an attack scenario in an enterprise network. The lab consists of a pfSense firewall/router to manage the internal network, a Windows Server domain controller, two Windows 10 client machines, a Kali Linux machine as the attacking platform and an Ubuntu machine hosting a Wazuh server with Suricata for real-time defensive monitoring. The lab provides a controlled environment for exploring common vulnerabilities, defensive methods and attacks within a Windows domain network.

## Skills Learned

## Lab Diagram
<img width="773" height="758" alt="Host machine" src="https://github.com/user-attachments/assets/e608d104-711e-4896-afe1-01107ca1d433" />

For the sake of this lab the attacker machine was put into the same subnet as the Active Directory environment to simplify the initial access. While this isn't completely realistic, placing the Kali Linux machine directly on the AD subnet allows for me to focus on post-compromise attacks.

  #
## pfSense Setup

![Capture(interfaces)](https://github.com/user-attachments/assets/558f18ec-d43d-4d98-bd97-898da54f58a8)

The four network interfaces: WAN, LAN, SPAN_PORT and AD_LAB. 
#
![WAN rules](https://github.com/user-attachments/assets/10df3a98-59d7-4d5a-bb04-a7dddfb013c9)

Rules for the WAN interface blocks private networks and bogon networks, ensuring only legitimate traffic is let into the network.
#
![LAN rules](https://github.com/user-attachments/assets/f84e196f-c1f7-4bc0-a2bc-73193e3a8347)

Rules for LAN interface blocks LAN to host traffic to isolate the VM from the host but is allowed to communicate with the other subnets and access internet.
#
![AD_LAB rules](https://github.com/user-attachments/assets/fc52baeb-fcc2-47ce-aecb-14988eee6a14)

Rules for AD_LAB interface allows packets to the default gateway and any public ip address for internet access but blocks everything else.
#
![bridges](https://github.com/user-attachments/assets/f02da5b5-2914-4502-9e5f-f26937c0d1cb)
The SPAN is configured using bridges to the LAN and AD_LAB interfaces to mirror their traffic.

  #
## Active Directory Setup

![DNS servers](https://github.com/user-attachments/assets/04ed063e-4863-4321-a95b-7a352092668a)

Domain Controller (10.80.80.2) set as the internal DNS server and default gateway (10.80.80.1) is set as external DNS server. 

![address pool](https://github.com/user-attachments/assets/11003b03-2026-4b0c-a0d7-c85e28e7a40b)

Address pool for DHCP server set to 10.80.80.3 - 10.80.80.254 

<img width="459" height="739" alt="ADdiagram" src="https://github.com/user-attachments/assets/8f7ff13e-fb3b-4fdc-8a94-472b66ebe7a3" />

 visual representation of the AD_LAB interface.   

![Users and groups](https://github.com/user-attachments/assets/7fa0da7f-a7b7-431f-be7f-467ab753dfa7)

Populated the Active Directory with users and groups.
  #
  
## Attacking the domain

