## Phase 3 --- VLAN Configuration

## 1. Phase 3 objectives

The objective of this phase was to logically separate university devices
into different Layer 2 broadcast domains using Virtual Local Area
Networks (VLANs).

The network uses one physical Cisco 2960 switch, with separate VLANs
for:

-   Students --- VLAN 10
-   Staff --- VLAN 20
-   University servers --- VLAN 30
-   Network management --- VLAN 99

VLANs provide Layer 2 segmentation. They do not, by themselves, provide
all the access-control rules required for a secure campus network.
Inter-VLAN routing and traffic restrictions will be configured in later
phases.

## 2. VLAN and IP addressing plan

  -------------------------------------------------------------------------------------
            VLAN ID VLAN name     Purpose          Planned subnet      Planned gateway
  ----------------- ------------- ---------------- ------------------- ----------------
                 10 STUDENTS      Student          `192.168.10.0/24`   `192.168.10.1`
                                  workstations                         

                 20 STAFF         Staff            `192.168.20.0/24`   `192.168.20.1`
                                  workstations                         

                 30 SERVERS       University       `192.168.30.0/24`   `192.168.30.1`
                                  server                               

                 99 MANAGEMENT    Network-device   `192.168.99.0/24`   `192.168.99.1`
                                  management                           
  -------------------------------------------------------------------------------------

The gateway addresses are planned for Phase 4. The switch management
interface is assigned `192.168.99.2/24` in this phase.

## 3. Switch port allocation

  -----------------------------------------------------------------------------------
  Switch interface     Connected device     Description                          VLAN
  -------------------- -------------------- ------------------- ---------------------
  FastEthernet0/1      PC0                  STUDENT_PC_01                          10

  FastEthernet0/2      PC1                  STUDENT_PC_02                          10

  FastEthernet0/3      PC2                  STAFF_PC_01                            20

  FastEthernet0/4      PC3                  STAFF_PC_02                            20

  FastEthernet0/5      Server0              UNIVERSITY_SERVER                      30

  GigabitEthernet0/1   R1                   CONNECTION_TO_R1                    Trunk
                       GigabitEthernet0/0                       
  -----------------------------------------------------------------------------------

VLAN 99 is used for switch management. No end-user access port is
assigned to VLAN 99.

## 4. Confirm the router-switch physical link

The intended cable connection is:

``` text
R1 GigabitEthernet0/0  <---->  SW1 GigabitEthernet0/1
```

On `SW1`, the uplink can be enabled and described with:

``` cisco
enable
configure terminal
interface gigabitEthernet 0/1
no shutdown
description CONNECTION_TO_R1
end
```

On `R1`, the connected interface was previously verified as:

``` text
GigabitEthernet0/0     unassigned     YES unset  up  up
```

This indicates that the router interface is enabled and its link
protocol is up. The switch-side interface can be checked with:

``` cisco
show interfaces gigabitEthernet 0/1 status
show interfaces gigabitEthernet 0/1
```

A working uplink should report a connected state and an up/up interface.

## 5. Create VLANs on SW1

Open `SW1` in Packet Tracer, select the CLI tab, and enter global
configuration mode:

``` cisco
enable
configure terminal
```

Create the Students VLAN:

``` cisco
vlan 10
name STUDENTS
exit
```

Create the Staff VLAN:

``` cisco
vlan 20
name STAFF
exit
```

Create the Servers VLAN:

``` cisco
vlan 30
name SERVERS
exit
```

Create the Management VLAN:

``` cisco
vlan 99
name MANAGEMENT
exit
```

These commands create the VLANs in the switch's VLAN database and assign
each VLAN a descriptive name.

## 6. Assign access ports to the correct VLANs

An access port carries traffic for one assigned VLAN.

### 6.1 Student workstations --- VLAN 10

``` cisco
interface range fastEthernet 0/1-2
switchport mode access
switchport access vlan 10
exit
```

### 6.2 Staff workstations --- VLAN 20

``` cisco
interface range fastEthernet 0/3-4
switchport mode access
switchport access vlan 20
exit
```

### 6.3 University server --- VLAN 30

``` cisco
interface fastEthernet 0/5
switchport mode access
switchport access vlan 30
exit
```

The `switchport mode access` command makes the port an access port. The
`switchport access vlan` command assigns that port to the specified
VLAN.

## 7. Configure the router-switch trunk

The router will eventually route traffic between the VLANs using one
physical router interface. The switch-to-router connection must
therefore carry traffic for multiple VLANs.

On `SW1`, configure GigabitEthernet0/1 as a trunk:

``` cisco
interface gigabitEthernet 0/1
switchport mode trunk
switchport trunk allowed vlan 10,20,30,99
no shutdown
exit
```

The trunk uses IEEE 802.1Q VLAN tagging. The allowed VLAN list specifies
which VLANs may traverse this link.

On a Cisco 2960, 802.1Q is the supported trunk encapsulation and is
normally selected automatically. A command such as
`switchport trunk encapsulation dot1q` is not required and may be
rejected by this switch model.

## 8. Configure the switch management SVI

A Switch Virtual Interface (SVI) provides a Layer 3 IP interface for
managing the Layer 2 switch.

Configure the VLAN 99 SVI:

``` cisco
interface vlan 99
ip address 192.168.99.2 255.255.255.0
no shutdown
exit
```

The management IP address is:

-   Address: `192.168.99.2`
-   Subnet mask: `255.255.255.0`
-   VLAN: `99`
-   Planned default gateway: `192.168.99.1` (to be configured after the
    router's VLAN 99 interface is ready).

The SVI's line protocol can come up when VLAN 99 exists and has an
active Layer 2 path on the switch, such as the operational trunk
carrying VLAN 99. If the SVI remains down, check the VLAN, trunk state,
and allowed VLAN list.

## 9. Save the configuration

After configuration, save the switch:

``` cisco
end
copy running-config startup-config
```

Press Enter if prompted to confirm the destination filename.

Saving copies the active running configuration into the startup
configuration so it can be loaded after a device reload.

## 10. Verification commands

The following commands are used on `SW1` to verify Phase 3.

### 10.1 Verify VLANs and access-port membership

``` cisco
show vlan brief
```

Expected membership:

    VLAN Name         Expected access ports
  ------ ------------ --------------------------
      10 STUDENTS     Fa0/1, Fa0/2
      20 STAFF        Fa0/3, Fa0/4
      30 SERVERS      Fa0/5
      99 MANAGEMENT   No end-user access ports

Trunk ports are not normally listed as access-port members in the same
way as the ports above.

### 10.2 Verify trunk operation

``` cisco
show interfaces trunk
```

Expected checks:

-   `Gi0/1` appears as a trunking interface.
-   The trunk uses 802.1Q.
-   VLANs `10,20,30,99` are allowed on the trunk.
-   The VLANs are active and forwarding as expected.

### 10.3 Verify switch interface and management IP status

``` cisco
show ip interface brief
```

Expected management SVI details:

``` text
Vlan99    192.168.99.2    YES manual    up    up
```

The exact formatting can vary by Packet Tracer IOS version. If `Vlan99`
is down, check that VLAN 99 exists and that an active switch port or
operational trunk carries it.

### 10.4 Other useful checks

``` cisco
show interfaces status
show running-config
show interfaces gigabitEthernet 0/1
```

These commands help inspect port connectivity, configuration commands,
and trunk-interface state.

## 11. Security significance of VLAN segmentation

VLAN segmentation improves the network design by separating groups into
different Layer 2 broadcast domains.

For this university:

-   Student broadcasts remain within the Students VLAN.
-   Staff broadcasts remain within the Staff VLAN.
-   Server traffic is separated into the Servers VLAN.
-   Network management is placed in a dedicated Management VLAN.

However, VLANs are not a complete security policy. A router or Layer 3
device can route between VLANs once inter-VLAN routing is configured.
Access Control Lists (ACLs) will be needed to define which groups may
access particular services and networks.

## 12. Phase 3 completion summary

  -----------------------------------------------------------------------
  Task                                Status
  ----------------------------------- -----------------------------------
  Create VLAN 10 --- STUDENTS         User reports Phase 3 is in line

  Create VLAN 20 --- STAFF            User reports Phase 3 is in line

  Create VLAN 30 --- SERVERS          User reports Phase 3 is in line

  Create VLAN 99 --- MANAGEMENT       User reports Phase 3 is in line

  Assign Fa0/1--Fa0/2 to VLAN 10      User reports Phase 3 is in line

  Assign Fa0/3--Fa0/4 to VLAN 20      User reports Phase 3 is in line

  Assign Fa0/5 to VLAN 30             User reports Phase 3 is in line

  Configure Gi0/1 as a trunk          User reports Phase 3 is in line

  Configure VLAN 99 SVI as            User reports Phase 3 is in line
  `192.168.99.2/24`                   

  Save and verify configuration       User reports Phase 3 is in line
  -----------------------------------------------------------------------

**Phase outcome:** The user reports that the Phase 3 configuration and
verification checks are in line. The next implementation phase is
Router-on-a-Stick inter-VLAN routing.

## 13. Next phase --- Phase 4: Router-on-a-Stick inter-VLAN routing

In Phase 4, the physical router interface `R1 GigabitEthernet0/0` will
be configured with logical subinterfaces for the VLANs.

  Router subinterface     802.1Q VLAN Planned gateway IP
  --------------------- ------------- --------------------
  G0/0.10                          10 `192.168.10.1/24`
  G0/0.20                          20 `192.168.20.1/24`
  G0/0.30                          30 `192.168.30.1/24`
  G0/0.99                          99 `192.168.99.1/24`

The physical router interface will carry tagged traffic for these VLANs.
Each subinterface will act as the default gateway for its respective
VLAN.

After configuration, end devices will receive IP addresses and default
gateways in their own subnets. Connectivity tests will confirm that
routing works. Security restrictions between VLANs will be introduced in
a later phase using ACLs.

