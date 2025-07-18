# Active-Directory-Lab-In-VirtualBox

## Objective
The goal of this lab is to build, configure and exploit an Active Directory environment to simulate an attack scenario in an enterprise network. The lab consists of a pfSense firewall/router to manage the internal network, a Windows Server domain controller, two Windows 10 client machines, a Kali Linux machine as the attacking platform and a Wazuh SIEM machine with Suricata for real-time defensive monitoring. The lab provides a controlled environment for exploring common vulnerabilities, defensive methods and attacks within a Windows domain network.

## Skills Learned

## pfSense setup

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
The SPAN interface doesn't have any firewall rules but has bridges to the LAN and AD_LAB interfaces to mirror their traffic.


