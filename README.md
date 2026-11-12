# Secure University Campus Network

## Project Documentation --- Phase 1 and Phase 2

**Project type:** Cisco Packet Tracer networking and cybersecurity lab\
**Environment:** Cisco Packet Tracer\
**Document version:** 1.0

------------------------------------------------------------------------

## 1. Project overview

The **Secure University Campus Network** project designs and implements
a segmented university network using Cisco Packet Tracer. The simulated
university has student devices, staff devices, and a university server.
The network will be developed in stages to demonstrate core networking
concepts and practical security controls.

The project aims to:

-   Build a working campus network topology.
-   Separate students and staff using Virtual LANs (VLANs).
-   Enable communication between approved networks through routing.
-   Provide services such as DHCP and DNS.
-   Restrict unauthorized traffic using Access Control Lists (ACLs).
-   Secure network-device administration using local authentication and
    SSH.
-   Test connectivity and security controls and document the results.

This is a simulation and learning project. Packet Tracer represents
network devices and their behavior; it is not a replacement for testing
on production equipment.

## 2. Planned network design

The planned logical design is:

``` text
                  Internet / ISP (simulated)
                            |
                     Edge Router (R1)
                            |
                     Core Switch (SW1)
                     /      |       \
              Student PCs Staff PCs University Server
                VLAN 10    VLAN 20    Server network
```

The initial departmental addressing plan is:

  -----------------------------------------------------------------------
  Network                 Purpose                 Planned subnet
  ----------------------- ----------------------- -----------------------
  VLAN 10                 Students                `192.168.10.0/24`

  VLAN 20                 Staff                   `192.168.20.0/24`

  VLAN 99                 Network management      To be defined
                          (planned)               

  Server network          University services     To be defined
                          (placement to be        
                          finalized)              
  -----------------------------------------------------------------------

The addressing plan is preliminary. IP addresses, gateways, server
placement, and VLAN configuration will be implemented and verified in
later phases.

## 3. Equipment and Packet Tracer devices

The initial topology uses these devices:

  ------------------------------------------------------------------------
  Device label     Packet Tracer                 Quantity Purpose
                   device                                 
  ---------------- ---------------- --------------------- ----------------
  R1               Cisco 2911                           1 Routing between
                   Router                                 networks

  SW1              Cisco 2960                           1 Connecting end
                   Switch                                 devices and VLAN
                                                          segmentation

  PC0--PC3         PC                                   4 Two student
                                                          endpoints and
                                                          two staff
                                                          endpoints

  Server0          Server                               1 University
                                                          network services
                                                          in a later phase
  ------------------------------------------------------------------------

### 3.1 How to add the devices

1.  Open Cisco Packet Tracer and create a new workspace.
2.  From **Network Devices → Routers**, select a **2911** router and
    place it on the workspace.
3.  From **Network Devices → Switches**, select a **2960** switch and
    place it below the router.
4.  From **End Devices**, add four PCs and one Server.
5.  Arrange the devices so the router is above the switch and the end
    devices are below it.
6.  Keep the device labels visible. The labels used in this project are
    `R1`, `SW1`, `PC0`, `PC1`, `PC2`, `PC3`, and `Server0`.

### 3.2 Physical cabling

Select **Connections** (the lightning-bolt icon), then choose **Copper
Straight-Through**.

Connect the devices as follows:

  From device   Interface            To device   Interface
  ------------- -------------------- ----------- --------------------
  R1            GigabitEthernet0/0   SW1         GigabitEthernet0/1
  PC0           FastEthernet0        SW1         FastEthernet0/1
  PC1           FastEthernet0        SW1         FastEthernet0/2
  PC2           FastEthernet0        SW1         FastEthernet0/3
  PC3           FastEthernet0        SW1         FastEthernet0/4
  Server0       FastEthernet0        SW1         FastEthernet0/5

Wait for link indicators to settle. A connected endpoint should normally
show an active link after the relevant interfaces have come up.

### 3.3 Save the Packet Tracer project

Save the Packet Tracer workspace as:

``` text
Secure-University-Network.pkt
```


------------------------------------------------------------------------

# Phase 1 --- Physical topology

## 4. Phase 1 objective

The objective of Phase 1 was to create the physical/logical device
layout and connect the router, switch, PCs, and server.

## 5. Phase 1 implementation

The following devices were added:

-   One Cisco 2911 router (`R1`).
-   One Cisco 2960 switch (`SW1`).
-   Four PCs (`PC0`--`PC3`).
-   One server (`Server0`).

The devices were connected using copper straight-through cables
according to the cabling table in Section 3.2.

### 5.1 Endpoint roles

  Device    Planned role            Switch port
  --------- ----------------------- -------------
  PC0       Student workstation 1   Fa0/1
  PC1       Student workstation 2   Fa0/2
  PC2       Staff workstation 1     Fa0/3
  PC3       Staff workstation 2     Fa0/4
  Server0   University server       Fa0/5

At this stage, the PCs and server are physically connected but have not
yet been assigned their departmental VLANs or configured IP addresses.

## 6. Phase 1 outcome

**Status: Completed.**

The physical topology was created successfully. The devices and their
cable connections form the base for the configuration work in Phase 2.

![Alternative Text](assets/phase1.png)


------------------------------------------------------------------------

### 101.1 Important security and implementation notes

-   Passwords in this document are lab examples, not production
    credentials.
-   `service password-encryption` is not a substitute for strong secret
    storage or secure authentication.
-   SSH remote administration is not complete until the switch has a
    reachable management IP address and an SSH connection is tested.
-   The current network is still using the default VLAN 1 on the
    displayed switch ports. Departmental segmentation has not yet been
    implemented.
-   No IP addressing, DHCP, DNS, ACLs, or internet simulation has been
    configured yet.

------------------------------------------------------------------------



## 101. 3 Learning outcomes so far

By completing Phases 1 and 2, the project has introduced:

-   Basic campus network topology design.
-   Cisco Packet Tracer device selection and cabling.
-   Router and switch CLI navigation.
-   Cisco IOS user EXEC, privileged EXEC, and global configuration
    modes.
-   Device hostnames and interface descriptions.
-   Console and privileged-mode password configuration.
-   Local user creation.
-   SSH configuration prerequisites and VTY restrictions.
-   Interface activation and status verification.
-   Saving running configurations to startup configurations.

