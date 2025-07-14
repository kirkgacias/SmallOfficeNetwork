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


### PC1 Configuration:
```
Default Gateway: 192.168.1.254 (R1)
IP Address: 192.168.1.1
Subnet Mask: 255.255.255.0
```
<br>

![PC1](https://github.com/user-attachments/assets/a6ecaf45-1893-492a-8258-c41d07656b75)

### PC2 Configuration:
```
Default Gateway: 192.168.3.254 (R3)
IP Address: 192.168.3.1
Subnet Mask: 255.255.255.0
```
<br>

![FIX](https://github.com/user-attachments/assets/cdd53c81-3dc0-4ac8-b1c2-041be3b8c7d6)



## Router Configurations
To configure the routers, I will first enter privileged EXEC mode using the `enable` command and then switch to global configuration mode. Next, we will set a hostname for each router and proceed to configure their interfaces. Each interface will be assigned its respective IP address, along with a description indicating its connection. Finally, we will activate the interfaces using the `no shutdown` command and repeat these steps for all interfaces.

<br>

![R1 config](https://github.com/user-attachments/assets/d9f5b4e6-2902-41f7-824a-ff458611f3a2)

### R1:
```
ENABLE
CONFIGURE TERMINAL
HOSTNAME R1
INTERFACE G0/1
IP ADDRESS 192.168.1.254 255.255.255.0
DESCRIPTION ## to SW1 ##
NO SHUTDOWN
INTERFACE G0/0
IP ADDRESS 192.168.12.1 255.255.255.0
DESCRIPTION ## to R2 ##
NO SHUTDOWN
```


### R2:
```
ENABLE
CONFIGURE TERMINAL
HOSTNAME R2
INTERFACE G0/0
IP ADDRESS 192.168.12.2 255.255.255.0
DESCRIPTION ## to R1 ##
NO SHUTDOWN
INTERFACE G0/1
IP ADDRESS 192.168.13.2 255.255.255.0
DESCRIPTION ## to R3 ##
NO SHUTDOWN
```

### R3:
```
ENABLE
CONFIGURE TERMINAL
HOSTNAME R3
INTERFACE G0/0
IP ADDRESS 192.168.13.3 255.255.255.0
DESCRIPTION ## to R2 ##
NO SHUTDOWN
INTERFACE G0/1
IP ADDRESS 192.168.3.254 255.255.255.0
DESCRIPTION ## to SW2 ##
NO SHUTDOWN
```

## Static Route Configuration
Since R1 and R3 are not directly connected, I must configure static routes for proper communication. R2, being an intermediate router, requires routes to both networks.
### R1 Configuration:
```
IP ROUTE 192.168.3.0 255.255.255.0 192.168.12.2
```
![Static Route 1](https://github.com/user-attachments/assets/03274f00-1a2c-4cd3-8cd0-f7e37e894f82)

<br>

### R2 Configuration:
```
IP ROUTE 192.168.1.0 255.255.255.0 G0/0
IP ROUTE 192.168.3.0 255.255.255.0 192.168.13.3
```

![image](https://github.com/user-attachments/assets/b228c1e4-4d42-4c94-ab94-73f2187d8715)


### R3 Configuration:
```
IP ROUTE 192.168.1.0 255.255.255.0 192.168.13.2
```

![STATIC 3](https://github.com/user-attachments/assets/f6078a9a-7b74-4dc7-b45d-94d0bed663a6)

## Verification and Testing
### Check Routing Tables:
```
DO SHOW IP ROUTE
```
Each router should display the configured static routes along with directly connected routes.

### Ping Test from PC1 to PC2:
```
PING 192.168.3.1
```
The first ping may fail due to ARP resolution, but subsequent pings should succeed if configured correctly.

![ping](https://github.com/user-attachments/assets/74bb387e-ac7f-4f2d-91ec-31c750c5b0b0)


## Conclusion
By configuring static routes, I successfully enabled communication between PC1 and PC2 across multiple routers. This lab demonstrated fundamental routing principles essential for network engineering and CCNA certification.
