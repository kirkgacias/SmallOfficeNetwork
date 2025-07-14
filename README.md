<p align="center">
<img src="https://www.practicalnetworking.net/wp-content/uploads/2019/10/CCNA-Certification-Logo.jpg"/>
</p>

# CCNA: Small Office Network with Guest Access (Packet Tracer)

## Introduction
This lab models a small office network supporting three business units (Sales, Marketing, HR) plus shared server and guest segments. 
Built entirely in Cisco Packet Tracer, it uses a single ISR edge router (Router1) and a multilayer Catalyst 3560 switch to demonstrate core networking fundamentals: VLAN segmentation, DHCP provisioning, inter-VLAN routing, internet access simulation, and simple access control.

## Network Topology
In this star topology all local networks converge on the central switch for segmentation and then flow through the router for gateway and internet access.
<br>

<img width="1452" height="935" alt="TOPOLOGY" src="https://github.com/user-attachments/assets/91423430-65af-44f6-ba3c-a4467b31dc50" />

## VLAN Segmentation & DHCP Services

Five VLANs isolate traffic by function: Sales (10), Marketing (20), HR (30), Servers (40), and Guest (50). 
Each VLAN has its own /24 subnet and gateway on Router1 subinterfaces. DHCP pools on Router1 dynamically assign addresses, with excluded ranges reserved for infrastructure. 
The Catalyst switch’s trunk port carries all VLANs to the router, and access ports connect PCs and servers to their respective VLANs. This setup highlights address planning, router-on-a-stick configuration, and automated IP provisioning across multiple broadcast domains.

<img width="384" height="324" alt="vlan names" src="https://github.com/user-attachments/assets/639014e0-1ccd-4f22-8109-1d807a72dabe" />


<br>


### DHCP POOL Configuration:
```
Router1(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.10
Router1(config)# ip dhcp excluded-address 192.168.20.1 192.168.20.10
Router1(config)# ip dhcp excluded-address 192.168.30.1 192.168.30.10
Router1(config)# ip dhcp excluded-address 192.168.40.1 192.168.40.10
Router1(config)# ip dhcp excluded-address 192.168.50.1 192.168.50.10

Router1(config)# ip dhcp pool SALES
Router1(dhcp-config)# network 192.168.10.0 255.255.255.0
Router1(dhcp-config)# default-router 192.168.10.1
Router1(dhcp-config)# dns-server 8.8.8.8
Router1(dhcp-config)# exit

Router1(config)# ip dhcp pool MARKETING
Router1(dhcp-config)# network 192.168.20.0 255.255.255.0
Router1(dhcp-config)# default-router 192.168.20.1
Router1(dhcp-config)# dns-server 8.8.8.8
Router1(dhcp-config)# exit

Router1(config)# ip dhcp pool HR
Router1(dhcp-config)# network 192.168.30.0 255.255.255.0
Router1(dhcp-config)# default-router 192.168.30.1
Router1(dhcp-config)# dns-server 8.8.8.8
Router1(dhcp-config)# exit

Router1(config)# ip dhcp pool SERVERS
Router1(dhcp-config)# network 192.168.40.0 255.255.255.0
Router1(dhcp-config)# default-router 192.168.40.1
Router1(dhcp-config)# dns-server 8.8.8.8
Router1(dhcp-config)# exit

Router1(config)# ip dhcp pool GUEST
Router1(dhcp-config)# network 192.168.50.0 255.255.255.0
Router1(dhcp-config)# default-router 192.168.50.1
Router1(dhcp-config)# dns-server 8.8.4.4
Router1(dhcp-config)# exit
```
<br>

### Routing & Internet Access:

Inter-VLAN forwarding is enabled by router-on-a-stick subinterfaces on Router1, and a default static route sends all non-internal traffic to the simulated ISP (Cloud-PT). Devices in each VLAN can reach the internet while still communicating across VLAN boundaries. 

### ROAS Configuration:
```
Router1(config)# interface GigabitEthernet0/0/1.10
Router1(config-subif)# encapsulation dot1Q 10
Router1(config-subif)# ip address 192.168.10.1 255.255.255.0
Router1(config-subif)# exit

Router1(config)# interface GigabitEthernet0/0/1.20
Router1(config-subif)# encapsulation dot1Q 20
Router1(config-subif)# ip address 192.168.20.1 255.255.255.0
Router1(config-subif)# exit

Router1(config)# interface GigabitEthernet0/0/1.30
Router1(config-subif)# encapsulation dot1Q 30
Router1(config-subif)# ip address 192.168.30.1 255.255.255.0
Router1(config-subif)# exit

Router1(config)# interface GigabitEthernet0/0/1.40
Router1(config-subif)# encapsulation dot1Q 40
Router1(config-subif)# ip address 192.168.40.1 255.255.255.0
Router1(config-subif)# exit

Router1(config)# interface GigabitEthernet0/0/1.50
Router1(config-subif)# encapsulation dot1Q 50
Router1(config-subif)# ip address 192.168.50.1 255.255.255.0
Router1(config-subif)# exit
```

<img width="596" height="105" alt="default route in router" src="https://github.com/user-attachments/assets/046b5d8b-84a8-4f92-8cb9-11bf80671872" />

## Basic Access Control with ACLs
A simple ACL on the Guest VLAN subinterface prevents guest hosts from accessing internal departmental networks while permitting all other traffic:

```
ip access-list extended GUEST_RESTRICT
  deny ip 192.168.50.0 0.0.0.255 192.168.10.0 0.0.0.255
  deny ip 192.168.50.0 0.0.0.255 192.168.20.0 0.0.0.255
  deny ip 192.168.50.0 0.0.0.255 192.168.30.0 0.0.0.255
  permit ip any any
interface GigabitEthernet0/0/1.50
  ip access-group GUEST_RESTRICT in
```


<br>

![R1 config](https://github.com/user-attachments/assets/d9f5b4e6-2902-41f7-824a-ff458611f3a2)


## Conclusion
This lab covers VLAN segmentation, DHCP, inter-VLAN routing, internet gateway setup and ACL security in a small office topology. 
