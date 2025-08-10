# Active-Directory-Lab-In-VirtualBox

## Objective
The goal of this lab is to build, configure and exploit an Active Directory environment to simulate an attack scenario in an enterprise network. The lab consists of a pfSense firewall/router to manage the internal network, a Windows Server domain controller, two Windows 10 client machines, a Kali Linux machine as the attacking platform and an Amazon Linux machine hosting a Wazuh server with Suricata for real-time defensive monitoring. The lab provides a controlled environment for exploring common vulnerabilities, defensive methods and attacks within a Windows domain network.

## Tools Used
- **Virtualization**: VirtualBox
- **Firewall & Routing**: pfSense
- **Domain Services**: Windows Server 2019 (AD DS, DNS, DHCP)
- **Clients**: Windows 10
- **Attack Platform**: Kali Linux
- **Monitoring**: Wazuh (SIEM), Suricata (IDS)
- **Tools**: Nmap, Impacket, BloodHound, John the Ripper, testmyids.com

## Skills Demonstrated
- Configured Windows Server 2019 as Domain Controller (AD DS, DNS, DHCP)
- Created pfSense firewall rules to isolate subnets and manage internal network
- Deployed and configured Wazuh SIEM for real-time log collection and analysis
- Integrated Suricata IDS for network-level intrusion detection through a SPAN port
- Performed reconnaissance and enumeration with Nmap and RID cycling
- Exploited users without Kerberos authentication with Impacket’s GetNPUsers
- Simulated detection of malicious activities with NMAP and testmyids.com

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
#
![address pool](https://github.com/user-attachments/assets/11003b03-2026-4b0c-a0d7-c85e28e7a40b)

Address pool for DHCP server set to 10.80.80.3 - 10.80.80.254 
#
<img width="459" height="739" alt="ADdiagram" src="https://github.com/user-attachments/assets/8f7ff13e-fb3b-4fdc-8a94-472b66ebe7a3" />

 visual representation of the AD_LAB interface.   
#
![Users and groups](https://github.com/user-attachments/assets/7fa0da7f-a7b7-431f-be7f-467ab753dfa7)

Populated the Active Directory with users and groups.
  #
  
## Attacking the domain
![Capture 1 (nmap)](https://github.com/user-attachments/assets/2e67e464-8130-4236-87cb-eb26088d1144)
Getting the root domain name using NMAP on the Kali Linux machine. 
  
  #
![RID bruteforce](https://github.com/user-attachments/assets/d0ce165d-2c0d-46b1-9a8d-e458cb3715a1)
Using NULL session RID cycling to gather usernames and groups.
  
  #
![impacket script](https://github.com/user-attachments/assets/fe60a10b-1147-4e2c-86a6-dbdb64813571)
Using Impacket GetNPUsers.py to find users without Kerberos authentication enabled and their password hash.

  #
![john](https://github.com/user-attachments/assets/29df2708-a8d8-4682-be2e-20fe2f1ebd2c)
Password cracked using John the Ripper and a password list.

  #
![bloodhound-python](https://github.com/user-attachments/assets/755c7685-4657-4152-9365-f2b9f0081e93)
Bloodhound-python (LDAP enumeration) and the cracked password used to collect all data on the domain and store the information in json files.

  #
![bloodhound capture](https://github.com/user-attachments/assets/3ec1e874-b7ba-4d39-a019-b21587b49d09)
Visual representation of our user's groups and permissions accessed through Bloodhound's interface.

#
## Testing Wazuh and Suricata's detection

<img width="1249" height="606" alt="image" src="https://github.com/user-attachments/assets/c5dc76f1-fa99-47a8-b89e-e57e7d238b9b" />

#
During the previous portion of this lab I did not account for the fact that Suricata would not be able to detect traffic between two machines on the same subnet. For the sake of testing Wazuh and Suricata's detection through the SPAN port, a firewall rule was added to allow the Kali machine to communicate with the DC from the LAN interface.

#
![nmap](https://github.com/user-attachments/assets/242738f3-4f95-4099-8f65-3a1d54e1f071)

Here Nmap was used through Kali to portscan the DC.

  #
![listened port status](https://github.com/user-attachments/assets/acd03ef4-6368-44b3-9cb9-b87412a9ddbd)
Wazuh alert "listened ports status changed" triggered by NMAP scan.

  #
![testmyids](https://github.com/user-attachments/assets/d1a0845e-2bac-49b6-b648-0bed481334c9)
  
Using testmyids.com to test Suricata's intrusion detection capabilities at the network level.
  
#
![Checkmyids](https://github.com/user-attachments/assets/f5b0520f-78a6-4fb7-b901-191c8004520d)
The response from testmyids.com triggers these responses from Suricata.  
  
#

  

