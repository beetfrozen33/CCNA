
# ==I. Basic Device Configuration==

**Switch Boot Sequence**

- POST test CPU, DRAM
- Boot loader software in ROM
- CPU register (quantity of memory and speed)
- Initializes flash file system
- Load IOS Operation System Software Image

**Path to the file system**

```
c2960-lanbasek9-mz.150-2.SE/
```

**Name of the file system**

```
c2960-lanbasek9-mz.150-2.SE.bin
```

### Switch LED indicators

1. **System LED**
    
    - **Off**: System not powered on.
    - **Green**: System operational.
    - **Amber**: Powered but malfunctioning.
      
2. **RPS LED**
    
    - **Off**: RPS off/not connected.
    - **Green**: RPS connected & backup-ready.
    - **Blinking Green**: RPS connected but powering another device.
    - **Amber**: Standby or fault state.
    - **Blinking Amber**: Internal power failure, RPS active.
      
3. **Port Status LED**
    
    - **Off**: No link or administratively down.
    - **Green**: Link established.
    - **Blinking Green**: Data transmission.
    - **Alternating Green-Amber**: Link fault.
    - **Amber**: Port blocked (loop prevention).
    - **Blinking Amber**: Possible loop prevention.
      
4. **Port Duplex LED**
    
    - **Off**: Half-duplex.
    - **Green**: Full-duplex.
      
5. **Port Speed LED**
    
    - **Off**: 10 Mbps.
    - **Green**: 100 Mbps.
    - **Blinking Green**: 1000 Mbps.
      
6. **PoE Mode LED**
    
    - **Off**: No PoE mode/fault.
    - **Blinking Amber**: PoE fault.
    - **Green**: PoE mode active.
    - **Port LED Off**: No PoE.
    - **Port LED Green**: PoE active.
    - **Alternating Green-Amber**: Power denial (over-capacity).
    - **LED Blinking Amber**: PoE fault.
    - **LED Amber**: PoE disabled for port.

![[Pasted image 20230829154052.png]]

**Configure Full-Duplex, Speed,  on Interface**

```
S1# configure terminal
S1(config)# interface FastEthernet 0/1
S1(config-if)# duplex auto
S1(config-if)# speed auto
```
### Switch Reset Procedure

1. **Setup Connection**:
    
    - Connect a PC to the switch using a console cable.
    - Set up terminal emulation software for switch access.
      
2. **Power Down**:
    
    - Disconnect the switch's power cord.
      
3. **Initiate Reset**:
    
    - Reconnect power to the switch.
    - Within 15 seconds, as the System LED flashes green, press & hold the Mode button.
      
4. **Complete Reset**:
    
    - Keep holding the Mode button until the System LED turns amber briefly, then solid green.
    - Release the Mode button.
      
5. **Access Boot Loader**:
    
    - The boot loader's `switch:` prompt will appear on your terminal emulation software.

**Auto-MDIX (Automatic Medium-Dependent interface crossover)** 

- Either type of cable can be used to connect
- Speed and duplex are set in auto to operates correctly

```
S1(config-if)# mdix auto
```

**See if MDIX configured**

```
S1# show controllers ethernet-controller fa0/1 phy | include MDIX
```

**Switch Verification Commands**

![[Pasted image 20230830105813.png]]

**Troubleshooting Checks**

- Interface Up
- Protocol Up
- Checks Errors

![[Pasted image 20230830142641.png]]

**IOS file with "k9" support encryption (SSH)**

### Configure SSH

**1. Add domain name, generate pair key**

```
S1# show ip ssh
S1(config)# ip domain-name cisco.com
S1(config)# crypto key generate rsa 
How many bits in the modulus [512]: 1024
```

**2. Create Account**

```
S1(config)# username admin secret ccna
```

**3. Open virtual line, insert ssh and look at local username database**

```
S1(config)# line vty 0 15
S1(config-line)# transport input ssh
S1(config-line)# login local 
S1(config-line)# exit
```

**4. Enable SSH v2 (more secured)**

```
S1(config)# ip ssh version 2
```

**5. Show if SSH is running**

```
S1# show ssh 
%No SSHv1 server connections running. 
Connection  Version Mode Encryption Hmac         State       Username 
0           2.0     IN  aes256-cbc hmac-sha1 Session started   admin 
0           2.0     OUT aes256-cbc hmac-sha1 Session started   admin 
S1#
```

### Configure Basic Router

```
Router# configure terminal
Enter configuration commands, one per line. End with CNTL/Z.
Router(config)# hostname R1
```

```
R1(config)# enable secret class
```

```
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login 
R1(config-line)# exit 
```

```
R1(config)# line vty 0 4 
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit 
```

```
R1(config)# service password-encryption
R1(config)# banner motd #Authorized Access Only!#
```

**Create interface loopback**

```
R1(config)# interface loopback 0
R1(config-if)# ip address 10.0.0.1 255.255.255.0 
R1(config-if)# exit
```

**Verify IPv6 Link Local and Multicast Addresses**

![[Pasted image 20230831081346.png]]

### Filter show command

**Section**

```
R1# show running-config | section line vty
line vty 0 4 
  password 7 110A1016141D 
  login 
  transport input all
```

**Include**

```
R1# show ip interface brief | include up 
GigabitEthernet0/0/0 192.168.10.1 YES manual up up 
GigabitEthernet0/0/1 192.168.11.1 YES manual up up 
Serial0/1/0 209.165.200.225 YES manual up up
```

**Exclude**

```
R1# show ip interface brief | exclude unassigned 
Interface IP-Address OK? Method Status Protocol 
GigabitEthernet0/0/0 192.168.10.1 YES manual up up 
GigabitEthernet0/0/1 192.168.11.1 YES manual up up 
Serial0/1/0 209.165.200.225 YES manual up up
```

**Begin**

![[Pasted image 20230831082017.png]]

**Pipe**

```
R1#show interface | include G0/0/0|duplex
Full-duplex, 100Mb/s, media type is RJ45
Full-duplex, 100Mb/s, media type is RJ45
```

**History commands**

```
R1# terminal history size 200 
R1# show history
	show ip int brief 
	show interface g0/0/0 
	show ip route 
	show running-config 
	show history terminal history size 200
```

# ==II. Switching Concepts==

- **Ingress Traffic** : Enter
- **Egress Traffic** : Leave

### Switch Learning

##### Examining the Source MAC Address

- If MAC address does not exist in MAC table then MAC address and incoming port number are added to the table.

- If exist, updates the refresh timer for that entry. By default, entry in the table for five minutes. 

- If MAC address does exist in the table but on a different port ==> new entry.

##### Examining the destination MAC Address

- If the destination MAC address is in the table, it will forward the frame out of the specified port.

- If the destination MAC address is not in the table, the switch will forward the frame out all ports except the incoming port. This is called an unknown unicast. If the destination MAC address is a broadcast or a multicast, the frame is also flooded out all ports except the incoming port.

### Forwarding Methods

- **Store-and-forward switching** : Forwarding decision after received the entire frame and CRC
	- **Error Checking** : Error Free check
	- **Automatic buffering** : Switch Store the entire frame in order to support any Ethernet speed (when a 1 Gbps Ethernet cable is required then the switch can store in the buffer and send in 100 Mbps Ethernet cable) 

![[Pasted image 20230901112114.png]]

- **Cut-trough switching** : Forwarding decision after destination MAC address and port determined
	- **No Error Checking**
	
![[Pasted image 20230901112511.png]]

### Auto-Negocation

![[Pasted image 20230901113054.png]]

**Parameters to take in account for Network Collisions**

- Fast port speeds
- Fast internal switching
- Large frame buffers
- High port density 

# ==III. VLANs==

![[Pasted image 20230901200740.png]]

- Can be in the same VLAN without having the same switch
- Segment networks based on functions
- VLAN creates a **logical broadcast domain**

![[Pasted image 20230902091815.png]]

### VLANs benefits

- **Smaller broadcast domains**
- Improved Security
- Improved IT Efficiency
- Reduced Cost
- Better performance
- Simpler project and application management

### Type of VLANs

- **Default VLAN** : applied to all ports 

![[Pasted image 20230902092754.png]]

- **Data VLAN** : Separate user-generated traffic

- **Native VLAN** : Traffic from a VLAN must be tagged with its VLAN ID when it is send to another switch by trunk ports (4-byte tag) , in fact untagged traffic is place on the native VLAN.

- **Management VLAN** : Network Management including SSH, Telnet, HTTPS, HTTP and SNMP. VLAN 1 is configured as the management VLAN on a Layer 2 switch

- **Voice VLAN** : VoIP Traffic requires :
		- Assured Bandwidth to ensure voice quality
		- Transmission priority
		- Ability to be routed
		- Delay of less than 150 ms

![[Pasted image 20230902094603.png]]

### VLAN Trunks 802.1Q

- Point-to-point link between two networks that carries more than one VLAN.

![[Pasted image 20230902162823.png]]

**VLAN Tags fiels**

![[Pasted image 20230904093817.png]]

- **Type** - A 2-byte protocol ID (TPID) value. Ethernet is 0x8100.
- **User priority** - A 3-bit value that supports level or service implementation.
- **Canonical Format Identifier (CFI)** - A 1-bit identifier that enables Token Ring frames to be carried across Ethernet links.
- **VLAN ID (VID)** - A 12-bit VLAN identification number that supports up to 4096 VLAN IDs.

### Tagged Frames on the Native VLAN

- Control traffic sent on the Native VLAN should not be tagged, it will drops the frame
- Configure devices in order to not send tagged frames on the native VLAN
- Other vendors support tagged frames on the native VLAN include IP phones, servers, routers and non-Cisco switches.

## Untagged Frames on the Native VLAN

- Trunk port receives untagged frames it forwards to the native VLAN (if no devices associated or trunk ports it drop the frame)
- Default native VLAN is VLAN 1

**Untagged traffic**

![[Pasted image 20230904145118.png]]

### Voice VLAN Tagging

- QoS 
- Security policies

**Example : VLAN 150 voice traffic and VLAN 20 student data

![[Pasted image 20230904145521.png]]

![[Pasted image 20230904145713.png]]

**Erase VLAN Configuration file vlan.dat**

```
S1#delete vlan.dat
Delete filename [vlan.dat]?
Delete flash:/vlan.dat ? [confirm]
```

**Erase Startup-configuration**

```
S1# erase startup-config
```

**Show VLAN configuration**

![[Pasted image 20230904151333.png]]

**Normal Range VLANs (1 - 1005)**

- Small and medium-sized business 
- 1002? 1005 for legacy network technologies (Token Ring and Fiber Distributed Data Interface)
- VLAN 1, 1002 and 1005 automatically created and cannot be removed
- VLAN config in flash memory vlan.dat
- VLAN trunking protocol (VTP) helps synchronize VLAN database between switches

**Extended Range VLANs (1006 - 4094)**

- Used by service providers, global enterprises
- Configuration saved by default in running configuration
- Fewer VLAN features

## VLAN Creation Commands

```
Switch# configure terminal
Switch(config)# vlan 20
Switch(config-vlan)# name student
Switch(config-vlan)# end
```

![[Pasted image 20230906100503.png]]

**Access mode indicates the port belongs to a single VLAN**

```
S1# configure terminal
S1(config)# interface fa0/6
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 20
S1(config-if)# end
```

**Data and Voice VLAN**

![[Pasted image 20230906100849.png]]

```
S3(config)# vlan 20
S3(config-vlan)# name student
S3(config-vlan)# vlan 150
S3(config-vlan)# name VOICE
S3(config-vlan)# end
S3(config)# interface fa0/18
S3(config-if)# switchport mode access
S3(config-if)# switchport access vlan 20
S3(config-if)# mls qos trust cos
S3(config-if)# switchport voice vlan 150
S3(config-if)# end 
```

**Switchport access vlan forces the creation of a VLAN if it does not already exist**

## Verify VLAN Information

```
S1# show vlan brief
S1# show vlan id 80
S1# show vlan name student
S1# show vlan summary
Number of existing VLANs : 7 
Number of existing VTP VLANs : 7 
Number of existing extended VLANS : 0
```

**Confirm Interface has been correctly assigned to DATA and Voice VLAN**

```
S1# show interface fa0/18 switchport 
Name: Fa0/18
Switchport: Enabled
Administrative Mode: static access
Operational Mode: static access 
Administrative Trunking Encapsulation: dot1q 
Operational Trunking Encapsulation: native 
Negotiation of Trunking: Off 
Access Mode VLAN: 20 (student) 
Trunking Native Mode VLAN: 1 (default) 
Voice VLAN: 150 
Administrative private-vlan host-association: none 
(Output omitted)
```

**Put port back to default VLAN 1**

![[Pasted image 20230906102055.png]]

![[Pasted image 20230906102204.png]]

**Put port back to VLAN 20**

```
S1(config)# interface fa0/18
S1(config-if)# switchport mode access vlan 20
```

**Delete VLAN 20**

```
S1(config)# no vlan 20
```

*Delete a VLAN without reassign all member port to a different VLAN first will put ports on unable to communicate

**Delete Vlan.dat file**

```
S1(config)# delete flash:vlan.dat 
```

```
S1(config)# delete vlan.dat
```

*If the file has been moved it will not work 

## VLAN Trunks

```
S1# configure terminal
S1(config)# interface interface-id
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk vlan id
S1(config-if)# switchport trunk allowed vlan vlan-list
S1(config-if)# end
```

![[Pasted image 20230906122643.png]]

```
S1# configure terminal
S1(config)# interface F0/1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk vlan 99
S1(config-if)# switchport trunk allowed vlan 10,20,30,99
S1(config-if)# end
```

![[Pasted image 20230906122911.png]]

**Reset Trunk to default state**

```
S1(config)# interface fa0/1
S1(config-if)# no switchport trunk allowed vlan
S1(config-if)# no switchport trunk native vlan
S1(config-if)# end
```

![[Pasted image 20230906123109.png]]

**Trunk Command**

![[Pasted image 20230906135658.png]]

### Dynamic Trunking Protocol

- Negotiate trunking with a neighboring device
- Cisco proprietary protocol
- Other vendor don't support DTP

**Enable trunking from a Cisco switch and device don't support DTP**

```
S1(config-if)# switchport mode trunk
S1(config-if)# switchport nonegotiate
```

**Re-enable dynamic trunking protocol**

```
S1(config-if)# switchport mode dynamic auto
```

**DTP Commands**

![[Pasted image 20230906205120.png]]

![[Pasted image 20230906205309.png]]

**Verify DTP Mode**

![[Pasted image 20230906205421.png]]

**Set native vlan on trunk**

```
S1(config)# interface GigabitEthernet0/2
S1(config-if)# switchport trunk native vlan 100
```

# ==IV. Inter-VLAN Routing==

**Problem :** *Need of routers and layer 3 switch to enable connectivity with hosts in one VLAN and with hosts in another VLAN

- Forwarding Network Traffic from one VLAN to another VLAN
## 3 Ways

- **Legacy Inter-VLAN Routing :** Not scale well
- **Router-on-a-Stick :** acceptable option for small to medium-sized network
- **Layer 3 switch using switched virtual interfaces (SVIs)** : Most scalable options for medium to large organizations

### Legacy Inter-VLAN Routing

- Router with multiples interfaces

![[Pasted image 20230907091647.png]]

**S1 table**

![[Pasted image 20230907091743.png]]


- PC1 ping PC2  ==> default gateway 192.168.10.1 (R1) on G0/0/0  ==> routes to G0/0/1 to F0/12 port in VLAN 20 S1 ==> PC2.

- Not scalable because limited number of physical interfaces (1 interface for 1 Vlan)

### Router-on-a-Stick Inter-VLAN Routing

- Require one interface
- Not scable beyong 50 VLANs
- Software-based virtual Subinterfaces 

![[Pasted image 20230907092420.png]]

PC1 VLAN 10 ping PC3 VLAN 30 ==> R1 accepts tagged unicast traffic on VLAN 10 route to subinterfaces ==> S2 removes VLAN tag ==> PC3 F0/23.

### Inter-VLAN Routing on a Layer 3 Switch

- Layer 3 / Multi Layer Switches (operates at Layer 2 and Layer 3) and Switched Virtual Interfaces (SVI)
- SVIs create the same way that the management VLAN
- SVIs same functions than router interface

![[Pasted image 20230907094112.png]]

**Advantages**

- Faster than router-on-a-stick (hardware switched and routed)
- No need of external links
- Not limited to one Link because Layer 2 EtherChannels to increase bandwidth
- Latency lower because no need to leave the switch
- Deployed on campus LAN than routers

**Disadvantage**

- More expensive than Layer 2 switch

### Router-on-a-Stick Configuration

![[Pasted image 20230907102914.png]]

![[Pasted image 20230907102947.png]]

- **To enable devices to ping each other :**
	- Switches must be configured with VLANs and trunking
	- Router must be configured for inter-VLAN routing.
##### S1 VLAN and Trunking Configuration

- **Create and Name VLAN**

```
S1(config)# vlan 10
S1(config-vlan)# name LAN10 
S1(config-vlan)# exit 
S1(config)# vlan 20 
S1(config-vlan)# name LAN20 
S1(config-vlan)# exit 
S1(config)# vlan 99 
S1(config-vlan)# name Management 
S1(config-vlan)# exit 
S1(config)#
```

- **Create Management interface and default-gateway**

```
S1(config)# interface vlan 99 
S1(config-if)# ip add 192.168.99.2 255.255.255.0
S1(config-if)# no shut 
S1(config-if)# exit 
S1(config)# ip default-gateway 192.168.99.1
S1(config)#
```

- **Configure Access Ports**

```
S1(config)# interface fa0/6 
S1(config-if)# switchport mode access 
S1(config-if)# switchport access vlan 10
S1(config-if)# no shut 
S1(config-if)# exit 
S1(config)#
```

- **Configure Trunking Ports**

```
S1(config)# interface fa0/1 
S1(config-if)# switchport mode trunk 
S1(config-if)# no shut
S1(config-if)# exit 
S1(config)# interface fa0/5 
S1(config-if)# switchport mode trunk
S1(config-if)# no shut 
S1(config-if)# end
```

**S2 Configure is Similar**

```
S2(config)# vlan 10
S2(config-vlan)# name LAN10 
S2(config-vlan)# exit 
S2(config)# vlan 20 
S2(config-vlan)# name LAN20 
S2(config-vlan)# exit 
S2(config)# vlan 99 
S2(config-vlan)# name Management 
S2(config-vlan)# exit 
S2(config)# 
S2(config)# interface vlan 99
S2(config-if)# ip add 192.168.99.3 255.255.255.0 
S2(config-if)# no shut 
S2(config-if)# exit 
S2(config)# ip default-gateway 192.168.99.1 
S2(config)# interface fa0/18
S2(config-if)# switchport mode access 
S2(config-if)# switchport access vlan 20 
S2(config-if)# no shut 
S2(config-if)# exit 
S2(config)# interface fa0/1 
S2(config-if)# switchport mode trunk
S2(config-if)# no shut 
S2(config-if)# exit 
S2(config-if)# end
```

**R1 Subinterface Configuration**

- **Subinterface for each VLAN**

```
R1(config)# interface G0/0/1.10
R1(config-subif)# description Default Gateway for VLAN 10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip add 192.168.10.1 255.255.255.0
R1(config-subif)# exit
R1(config)#
R1(config)# interface G0/0/1.20 
R1(config-subif)# description Default Gateway for VLAN 20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip add 192.168.20.1 255.255.255.0
R1(config-subif)# exit
R1(config)#
R1(config)# interface G0/0/1.99
R1(config-subif)# description Default Gateway for VLAN 99
R1(config-subif)# encapsulation dot1Q 99
R1(config-subif)# ip add 192.168.99.1 255.255.255.0
R1(config-subif)# exit
R1(config)#
R1(config)# interface G0/0/1
R1(config-subif)# description Trunk link to S1
R1(config-subif)# no shutdown
```

**Troubleshoot Router-on-a-stick Configuration**

- show ip route : verif routes
- show ip interface brief : verif IP and up
- show interfaces : verif subinterfaces
- show interfaces trunk : verif trunk 

##### Layer 3 Switch Inter-VLAN Routing

**Capabilities :**

- Route from one VLAN to another multiple switched virtual interfaces (SVIs)
- Convert Layer 2 switchport to a Layer 3 interface

![[Pasted image 20230907133545.png]]
![[Pasted image 20230907133645.png]]

**Layer 3 Switch Configuration**

- **Create VLANs**

```
D1(config)# vlan 10
D1(config-vlan)# name LAN10
D1(config-vlan)# vlan 20
D1(config-vlan)# name LAN20
D1(config-vlan)# exit
```

- **Create SVI VLAN interfaces**

```
D1(config)# interface vlan 10
D1(config-if)# description Default Gateway SVI for 192.168.10.0/24
D1(config-if)# ip add 192.168.10.1 255.255.255.0
D1(config-if)# no shut
D1(config-if)# exit
D1(config)#
D1(config)# int vlan 20
D1(config-if)# description Default Gateway for 192.168.20.0/24
D1(config-if)# ip add 192.168.20.1 255.255.255.0
D1(config-if)# no shut
D1(config-if)# exit
```

- **Configure access ports**

```
D1(config)# interface GigabitEthernet1/0/6
D1(config)# description Access port to PC1
D1(config)# switchport mode access
D1(config)# switchport access vlan 10
D1(config)# exit
D1(config)#
D1(config-if)# interface GigabitEthernet1/0/18
D1(config-if)# description Access port to PC2
D1(config-if)# switchport mode access
D1(config-if)# switchport access vlan 20
D1(config-if)# exit
```

- **Enable IP routing (allow traffic to be exchanged between VLANs 10 and 20)**

```
D1(config)# ip routing
```

![[Pasted image 20230907134920.png]]

#### Routing Configuration on a Layer 3 Switch

- **Configure the routed port**

```
D1(config)# interface GigabitEthernet0/0/1
D1(config-if)# description routed Port Link to R1
D1(config-if)# no switchport
D1(config-if)# ip address 10.10.10.2 255.255.255.0
D1(config-if)# no shutdown
D1(config-if)# exit
```

- **Enable routing**

```
D1(config)# ip routing
```

- **Configure routing**

```
D1(config)# routeur ospf 10
D1(config)# network 192.168.10.0 0.0.0.255 area 0 
D1(config)# network 192.168.20.0 0.0.0.255 area 0
D1(config)# network 10.10.10.0 0.0.0.3 area 0
```

- **Verify routing**

![[Pasted image 20230907135523.png]]

- **Verify Connectivity**

```
C:\Users\PC1>ping 10.20.20.254
C:\Users\PC2>ping 10.20.20.254
```
#### Common Inter-VLAN Issues

- **Physical Layer : connected to the wrong port**

![[Pasted image 20230907144938.png]]

# ==V. STP Concepts==

**Spanning Tree Protocol - L2 Protocol**

- Create topology without loop

- STP send BPDU packet in order to get a root bridge

- Switch disabled ports and create a topology without loop

![[Pasted image 20230824185242.png]]

**Broadcast Storm**

- Abnormally high number of broadcasts overwhelming
### The Spanning Tree Algorithm

- **Select the Root Bridge**

![[Pasted image 20230911112959.png]]

- **Redundant Ports**

![[Pasted image 20230911113017.png]]

- **Loop-Free Topology**

![[Pasted image 20230911113201.png]]

- **Link Failure Causes Recalculation**

![[Pasted image 20230911114054.png]]
### Steps

- Elect the Root Bridge
- Elect the Root Ports
- Elect Designated Ports
- Elect alternate (blocked) ports

**Used of Bridge Protocol Data Units (BPDUs) to elect the root bridge, root ports, designated ports, and alternate ports**

**Each BPDU contains a bridge ID (BID) :**

![[Pasted image 20230911115723.png]]


- **Bridge Priority** (default Cisco Switch 32768) : Range from 0 to 61440, priority 0 takes precedence over all other bridge priorities

- **Extended System ID** : decimal value to identify VLAN for this BPDU

- **MAC Address** : When switches have same priority and same extended system ID, having the lowest MAC address value will have the lower BID

### STP Operations

1. **Elect Root Bridge**

- At first, switches declare themselves as the root bridge with their own BID set as the Root ID
- Trough exchange of BPDUs
- Switch with lowest BID become the root bridge

![[Pasted image 20230911221246.png]]

Priority = default bridge priority and VLAN so for VLAN 1 = 32768 + 1 = 32769

2. **Elect Root Ports**

**Root Path Cost** 

![[Pasted image 20230912102345.png]]

![[Pasted image 20230912102616.png]]

- Root port is the port closest to the root bridge in terms of overall cost (best path) to the root bridge
- Sum of all the port costs along the path to the root bridge

3. **Elect Designated Ports**

- All ports on the root bridge are designated ports

![[Pasted image 20230912103539.png]]

- All switch ports with end devices (hosts) attached are designated ports.

- The port with least-cost path to the root bridge is designated port, S2 and S3 have the same path cost to the root bridge, S2 has a lower BID it will be chosen as the designated port. 

4. **Elect Alternate (Blocked) Ports**

![[Pasted image 20230912104432.png]]

- If a port is not a root port or a designated port then it's a Alternate Port / Blocked Port

## Elect Root Port Particular Situations

- **1. Lower Sender BID**

F0/1 and F0/2 have the same path cost, the lower BID of the neighboring switches that enable connectivity with the root bridge will select the interface.

The bridge ID of S4 is lower than the bridge ID of S3 then, the interface in front of S4 will be chose f0/1 as Root Port and the other will be Alternate Port in our case.

![[Pasted image 20230912105039.png]]

- **2. Lowest Sender Port Priority**

The next step of comparison is port priority

![[Pasted image 20230912105521.png]]

In our case the priority is the same

- **3. Lowest Sender Port ID**

![[Pasted image 20230912105923.png]]

- The lower Sender Port ID is F0/1 then the f0/6 in front will be the root port and the other port the Alternate Port

### STP Convergence

- **Hello Timer** : Interval between BPDU, default 2 seconds (up 1 to 10 seconds)
- **Forward Delay Timer** : listening and learning state, default 15 seconds (up to 4 to 30 seconds)
- **Max Age Timer** : length of time switch wait before change STP topology, default 20 seconds (up to 6 to 40 seconds)


![[Pasted image 20230912110500.png]]

## Port State

![[Pasted image 20230912110558.png]]

![[Pasted image 20230912110734.png]]

## Per-VLAN Spanning Tree (PVST) 

- Version of STP where is a root bridge elected for each spanning tree instance

### Different Versions of STP

![[Pasted image 20230912112929.png]]

- Cisco switches running IOS 15.0 or later, run PVST+ by default

### RSTP Concept

- RSTP increases the speed of the recalculation of the spanning tree

![[Pasted image 20230912131604.png]]

- **STP and RSTP Port Roles**

![[Pasted image 20230912131745.png]]

- Backup port is a backup to a shared medium such as a hub

![[Pasted image 20230912131823.png]]

### PortFast and BPDU Guard

- **PortFast** : Transitions from blocking to forwarding state immediately to avoid 30 second delay it will avoid DHCP error, use on switch ports that connect to end devices

![[Pasted image 20230912141200.png]]

- Avoid PortFast connecting to another switch, will create spanning tree loop

- **BPDU Guard** : put the switch port in an errdisabled state on receipt of any BPDU

### Alternatives to STP

![[Pasted image 20230912142721.png]]



![[Pasted image 20230912143043.png]]



![[Pasted image 20230912143123.png]]

**MLAG (Multi System Link Aggregation)**
**SPB (Shortest Path Bridging)**
**TRILL (Transparent Interconnect of Lots of Links)**

# ==VI. EtherChannel==

## Link Aggregation

- Not blocked by STP, EtherChannel allows redundant links between devices
- Group multiple physical Ethernet links into one single logical link
- Fault-tolerance, Load Sharing, Increased bandwidth and redundancy

![[Pasted image 20230913084548.png]]

- By default, STP will block redundant links
- EtherChannel developed by Cisco

![[Pasted image 20230913084726.png]]

- When EtherChannel is configured, virtual interface is called a port channel
- Physical interfaces bundled together into a port channel interface
## Advantages

- Consistency
- No need to upgrade the link to a faster and more expensive connection to gain bandwidth
- Load balancing by source MAC, destination MAC, source IP, destination IP
- STP see one logical link
- Redundancy
- If a one physical link drop, it will not change the topology, no STP recalculation is required, EtherChannel remains functional it will only decreases the throughput 

## Implementation Restrictions

- Interface types cannot be mixed (not Fast Ethernet and Gigabit Ethernet on a single EtherChannel)
- 8 compatibly-configured Ethernet ports,  full-duplex bandwidth up to 800 Mbps (Fast EtherChannel) or 8 Gbps (Gigabit EtherChannel)
- Cisco Catalyst 2960 up to 6 Channel Interface
- Port config must be the same (trunk - trunk within the same native VLAN)
- Config as Layer 2 ports

![[Pasted image 20230913090104.png]]

## AutoNegotiation Protocols

- Port Aggregation Protocol (PAgP) is Cisco-Proprietary Protocol
- Link Aggregation Control Protocol (LACP) is IEEE specification

- These protocols allow ports with similar characteristics to form a channel trough dynamic negotiation with adjoining switches
- EtherChannel can be configure in manual 

## PagP Operation

- PAgP packets are sent between EtherChannel-capable ports to negotiate forming channel
- If PAgP identifies matches Ethernet links to groups the links into an EtherChannel, it will add the EtherChannel as a single port to the spanning tree
- Packets send every 30 seconds to checks configuration consistency, manages links additions and failure between switches

**Mode for PAgP**

- **On** : Interface channel without PAgP, don't exchange PAgP packet
- **PAgP desirable** : Initiates negotiations with other interfaces by sending PAgP packets
- **PAgP auto** : passive negotiating state, responds to the PAgP packets

**How it works**

![[Pasted image 20230913092701.png]]

![[Pasted image 20230913092647.png]]


```
S1(config)# interface range FastEthernet0/1 - 2
S1(config-if-range)# channel-group 1 mode on
Creating a port-channel interface Port-channel 1
S1(config-if-range)# exit
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk allowed vlan 1,2,20
S1(config-if)# no shutdown
```

## LACP Operation

It works in multivendor environments

- **On** : forces interface to channel without exchange LACP packets
- **LACP active** : port initiates negotiations with others ports by sending LACP packets
- **LACP passive** : passive negotiating state, responds to LCAP packets

![[Pasted image 20230913093404.png]]

![[Pasted image 20230913093357.png]]

## Configuration Guidelines

**EtherChannel Support** : Ethernet Interfaces must support EtherChannel
**Speed and duplex** : operate at the same speed and in the same duplex mode
**VLAN match** : same VLAN or configured as a trunk
**Range of VLANs** : EtherChannel supports same allowed range of VLANs

![[Pasted image 20230913101310.png]]

![[Pasted image 20230913101347.png]]

## LACP Configuration Example

![[Pasted image 20230913101532.png]]

1. **Select interfaces range**

2. **Shutdown interfaces**

3. **Create channel-group**

4. **Change Layer 2 to an port channel interface**

6. **No Shutdown interfaces**

```
S1(config)# interface range FastEthernet0/1 - 2
S1(config-if-range)# channel-group 1 mode active
Creating a port-channel interface Port-channel 1
S1(config-if-range)# exit
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk allowed vlan 1,2,20
S1(config-if)# no shutdown
```

**Command to see type of cable**

![[Pasted image 20230913103020.png]]

**Commands to see EtherChannel**

![[Pasted image 20230913105357.png]]

## Troubleshoot

- **Command Display status of port channel interface**

![[Pasted image 20230913112327.png]]

- **Command Display of EtherChannels**

![[Pasted image 20230913112524.png]]

- **Command Display information about specific port channel interface**

![[Pasted image 20230913113534.png]]

- **Command display role of the interface in EtherChannel**

![[Pasted image 20230913113632.png]]
## Common Issues

- Not in the same VLAN
- Not configured as Trunk
- Different native VLANs
- Different range of VLANs
- Dynamic negotiation options for PAgP and LACP (active or desirable)

# ==VII. DHCPv4==

### Dynamic Host Configuration Protocol

![[Pasted image 20230915140543.png]]

- Client / Server Mode
- Client communicates with DHCPv4 server
- DHCPv4 server leases IPv4 address
- Power Off release address

## Steps to get a Lease

- **DHCP Discover (DHCPDISCOVER)**
	- Broadcast Layer 2 and Layer 3 to find DHCPv4 Server

- **DHCP Offer (DHCPOFFER)**
	- IPv4 address lease to the MAC Address of the client

- **DHCP Request (DHCPREQUEST)**
	- Select the server for parameters and decline to any other DHCPv4 servers
	- Broadcast because in many enterprise networks use multiple DHCPv4 servers
	
- **DHCP Acknowledgment (DHCPACK)**
	- Server ICMP ping the address to ensure it's not used
	- ARP entry to the client lease

![[Pasted image 20230915141357.png]]

## Steps to Renew a Lease

- **DHCP Request (DHCPREQUEST)**
	- Before the lease expires, Client send DHCPREQUEST to DHCPv4 server
	- If DHCPACK is not received with a specified amount of time, broadcast another DHCPREQUEST to get other DHCPv4 servers

- **DHCP Acknowledgment (DHCPACK)**
	- Server verifies the lease information

![[Pasted image 20230915143648.png]]

## Steps to Configure DHCPv4 on router

![[Pasted image 20230915143834.png]]

- **Exclude IPv4 addresses** 

*Exclude IP of routers, servers, printers and others devices manually configured

```
Router(config)# ip dhcp excluded-address low-address [high-address]
```

- **Define a DHCPv4 pool name**

```
Router(config)# ip dhcp pool pool-name
```

- **Configure the DHCPv4 pool**

![[Pasted image 20230915150028.png]]

![[Pasted image 20230915150501.png]]

```
R1(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.9
R1(config)# ip dhcp excluded-address 192.168.10.254
R1(config)# ip dhcp pool LAN-POOL-1
R1(dhcp-config)# network 192.168.10.0 255.255.255.0
R1(dhcp-config)# default-router 192.168.10.1
R1(dhcp-config)# dns-server 192.168.11.5
R1(dhcp-config)# domain-name example.com
R1(dhcp-config)# end
```

### DHCPv4 Troubleshooting Commands

- **Show running-config**

```
R1# show running-config | section dhcp
ip dhcp excluded-address 192.168.10.1 192.168.10.9 
ip dhcp excluded-address 192.168.10.254 
ip dhcp pool LAN-POOL-1 
  network 192.168.10.0 255.255.255.0 
  default-router 192.168.10.1 
  dns-server 192.168.11.5 
  domain-name example.com
```

- **Show ip dhcp binding**

![[Pasted image 20230916163552.png]]

- **Show ip dhcp server statistics**

![[Pasted image 20230916163733.png]]

- **ipconfig /all on client** 

![[Pasted image 20230916163838.png]]

**Restart DHCP Service**

```
R1(config)# no service dhcp
R1(config)# service dhcp
```

### DHCPv4 Relay

- If a client is on another network than the DHCP Server, the router between networks must be configured to relay DHCPv4 messages

**Client Commands**

- **Release of the lease**

![[Pasted image 20230916165056.png]]

- **Renew the lease but the DHCP is on another network**

![[Pasted image 20230916165104.png]]

- **Configure Interface to relay DHCP messages to the DHCP server**

![[Pasted image 20230916165113.png]]

![[Pasted image 20230916165122.png]]

**Relay can also forward UDP services**

- Port 37 : Time
- Port 49 : TACACS
- Port 53 : DNS
- Port 67 : DHCP/BOOTP server
- Port 68 : DHCP/BOOTP client
- Port 69 : TFTP
- Port 137 : NetBIOS name service
- Port 138 : NetBIOS datagram service

**Configure interface to get DHCP configuration**

```
R1(config)#int g0/0
R1(config-if)# ip address dhcp
R1(config-if)# no shutdown
```

# ==VIII. SLAAC and DHCPv6

## IPv6 Host Configuration

- **2 Types of IPv6 Address**
	- Global Unicast Addresses (GUAs)
	- Link-Local Addresses (LLAs)

- Global Unicast Address is manually configured using "ipv6-address/prefix-length"

## IPv6 GUA Assignment

![[Pasted image 20230918143449.png]]

## Three RA Message Flags

- **A Flag** : SLAAC Only
- **O Flag** : Stateless DHCPv6 Server
- **M Flag** : DHCPV6 Server

![[Pasted image 20230918143950.png]]
## SLAAC

- Without the services of DHCPv6 Server
- No server maintains Network Address Information
- ICMPv6 RA messages sent from IPv6 router every 200 seconds provided addressing
- Router Solicitation (RS) are send by host to get an RA

## SLAAC Configuration

![[Pasted image 20230918145826.png]]

**Verify IPv6 Addresses**

![[Pasted image 20230918145839.png]]

**Enable IPv6 Routing**

![[Pasted image 20230918145920.png]]

**Verify SLAAC is Enabled**

![[Pasted image 20230918145934.png]]

## SLAAC Only Method

**In RA Message :**

- **A flag set to 1**

=> Create own GUA using prefix in the RA
=> Create own Interface ID with either Extended Unique Identifier method (EUI-64) or randomly generated

- **O flag set to 0**
- **M flag set to 0**

=> User information in the RA exclusively (prefix-length, DNS server, MTU and default gateway information)

![[Pasted image 20230918150756.png]]

- When client is configured to obtain automatically addressing it send RS messages to all-routers multicast address of ff02::2

![[Pasted image 20230918151115.png]]

## Host Process to Generate Interface ID

- **Randomly generated** : 64-bit interface ID randomly generated by the client. Use by Windows 10 hosts
- **EUI-64** : interface ID use 48-bit MAC address, insert fffe in the middle of the address, flip the seventh bit of the interface ID

![[ipv6.png]]

## Duplicate Address Detection (DAD)

- Ensure IPv6 GUA is unique
- Using ICMPv6

**How**

- Host sends an ICMPv6 Neighbor Sollicitation (NS) with a specially constructed multicast address (solicited-node multicast address)
- This address duplicates the last 24 bits of IPv6 address of the host.
- If no respond than it's unique else it will determine a new interface ID

## DHCPv6 Operations Steps

- **Server to Client** : DHCPv6 message UDP port 546
- **Client to Serveur** : DHCPv6 message UDP port 547

### 1. Host sends RS Message to IPv6-enabled routers

![[Pasted image 20230922134348.png]]
### 2. Router responds with an RA message to initiate communication with DHCPv6 server

![[Pasted image 20230922134421.png]]
### 3. Host sends a DHCPv6 SOLICIT message to ff02::1:2 (multicast all-DHCPv6 -servers)

![[Pasted image 20230922134510.png]]

### 4. DHCPv6 server responds with an ADVERSITE unicast message

![[Pasted image 20230922134651.png]]
### 5. Host responds to the DHCPv6 server

- **Stateless DHCPv6 client** : Client **creates IPv6 address** using prefix in the **RA message** and **self-generated Interface ID** and sends a **INFORMATION-REQUEST** message to the DHCPv6 server to get **additional configuration parameters (DNS, etc..)**

- **Stateful DHCPv6 client** : Client sends a **DHCPv6 REQUEST** to the DHCPv6 server to gain **all necessary IPv6 configuration parameters**

![[Pasted image 20230922135127.png]]
### 6. DHCPv6 server send a REPLY message

- DHCPv6 reply unicast message with configurations parameters

![[Pasted image 20230922135212.png]]

### Stateless DHCPv6 Operation

- Give IPv6 address of a DNS server
- Not maintaining any client state information

![[Pasted image 20230922135510.png]]
- A = 1  use SLAAC
- O = 1 use stateless DHCPv6 server
## Stateless DHCPv6 on an Interface

**Select Interface**

```
R1(config)# int g0/0/1
```

**Set O flag = 1**

```
R1(config-if)# ipv6 nd other-config-flag
```

**Check**

```
R1# show ipv6 interface g0/0/1 | begin ND 
	ND DAD is enabled, number of DAD attempts: 1 
	ND reachable time is 30000 milliseconds (using 30000) 
	ND advertised reachable time is 0 (unspecified) 
	ND advertised retransmit interval is 0 (unspecified) 
	ND router advertisements are sent every 200 seconds 
	ND router advertisements live for 1800 seconds 
	ND advertised default router preference is Medium 
	Hosts use stateless autoconfig for addresses. 
	Hosts use DHCP to obtain other configuration.
```

### Stateful DHCPv6 Operation

- All addressing information from stateful DHCPv6 server
- except default gateway address which is the link local address of the RA

![[Pasted image 20230922140641.png]]

- DHCPv6 RA message from routeur with O flag = 0, M flag = 1 
- PC1 sends a DHCPv6 SOLICIT message to find a stateful DHCPv6 server

### Stateful DHCPv6 on an Interface

**Select Interface**

```
R1(config)# int g0/0/1
```

**Set M flag = 1**

```
R1(config-if)# ipv6 nd managed-config-flag 
```

**Set A flag = 0**

```
R1(config-if)# ipv6 nd prefix default no-autoconfig
```

**Check**

```
R1(config)# show ipv6 interface g0/0/1 | begin ND
ND DAD is enabled, number of DAD attempts: 1 
ND reachable time is 30000 milliseconds (using 30000) 
ND advertised reachable time is 0 (unspecified) 
ND advertised retransmit interval is 0 (unspecified) 
ND router advertisements are sent every 200 seconds 
ND router advertisements live for 1800 seconds 
ND advertised default router preference is Medium 
Hosts use DHCP to obtain routable addresses.
```

### DHCPv6 Router Roles

- **DHCPv6 Server :** Stateless or Stateful DHCPv6 services

- **DHCPv6 Client :** Router interface acquires IPv6 IP configuration from DHCPv6 Server

- **DHCPv6 Relay Agent :** Router provides DHCPv6 forwarding when client and server are on different networks

### Configure Stateless DHCPv6 Server

![[Pasted image 20230922142021.png]]

**Enable IPv6 routing**

```
R1(config)# ipv6 unicast-routing
```

**Define a DHCPv6 pool name**

```
R1(config)# ipv6 dhcp pool IPV6-STATELESS
```

**Configure the DHCPv6 Pool**

```
R1(config-dhcpv6)# dns-server 2001:db8:acad:1::254
R1(config-dhcpv6)# domain-name example.com
```

**Bind DHCPv6 pool to an interface**

```
R1(config)# int g0/0/1
R1(config-if)# description Link to LAN
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 nd other-config-flag
R1(config-if)# ipv6 dhcp server IPV6-STATELESS
R1(config-if)# no shut
```

**Verify Host configuration**

![[Pasted image 20230922142540.png]]

### Configure a Stateless DHCPv6 Client

![[Pasted image 20230922142617.png]]

**Enable IPv6 routing**

```
R3(config)# ipv6 unicast-routing
```

**Configure client router to create an LLA**

```
R3(config)# int g0/0/1
R3(config-if)# ipv6 enable
```

**Configure client router to use SLAAC**

```
R3(config-if)# ipv6 address autoconfig
```

**Verify client router is assigned a GUA**

```
R3# show ipv6 interface brief
GigabitEthernet0/0/0 [up/up] 
unassigned 
GigabitEthernet0/0/1 [up/up] 
FE80::2FC:BAFF:FE94:29B1 
2001:DB8:ACAD:1:2FC:BAFF:FE94:29B1 
Serial0/1/0 [up/up] 
unassigned 
Serial0/1/1 [up/up] 
unassigned
```

**Verify client router received other DHCPv6 information**

![[Pasted image 20230922144003.png]]

### Configure Stateful DHCPv6 Server

![[Pasted image 20230922144250.png]]

**Enable IPv6 routing**

```
R1(config)# ipv6 unicast-routing
```

**Define a DHCPv6 pool name**

```
R1(config)# ipv6 dhcp pool IPV6-STATEFUL
```

**Configure the DHCPv6 pool**

```
R1(config-dhcpv6)# address prefix 2001:db8:acad:1::/64
R1(config-dhcpv6)# dns-server 2001:4860:4860::8888
R1(config-dhcpv6)# domain-name example.com
```

**Bind DHCPv6 pool to an interface**

```
R1(config)# int g0/0/1
R1(config-if)# description Link to LAN
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 nd managed-config-flag
R1(config-if)# ipv6 nd prefix default no-autoconfig
R1(config-if)# ipv6 dhcp server IPV6-STATEFUL
R1(config-if)# no shut
```

### Configure a Stateful DHCPv6 Client

![[Pasted image 20230922145155.png]]

**Enable IPv6 Routing**

```
R3(config)# ipv6 unicast-routing
```

**Configure client router to create an LLA**

```
R3(config)# int g0/0/1
R3(config-if)# ipv6 enable
```

**Configure client router to use DHCPv6**

```
R3(config-if)# ipv6 address dhcp
```

**Verify client router is assigned a GUA**

```
R3# show ipv6 interface brief
GigabitEthernet0/0/0 [up/up] 
unassigned 
GigabitEthernet0/0/1 [up/up] 
FE80::2FC:BAFF:FE94:29B1 
2001:DB8:ACAD:1:B4CB:25FA:3C9:747C 
Serial0/1/0 [up/up] 
unassigned 
Serial0/1/1 [up/up] 
unassigned
```

**Verify client router received other DHCPv6 information**

![[Pasted image 20230922145937.png]]

## DHCPv6 Server Verification Commands

- **DHCP pool name, number of clients, DNS server address, domain name**

![[Pasted image 20230930105300.png]]

- **Link-Local address of clients, global unicast address of clients**

![[Pasted image 20230930105412.png]]

## Configure DHCPv6 Relay Agent

![[Pasted image 20230930105734.png]]

```
R1(config)# int g0/0/1
R1(config-if)# ipv6 dhcp relay destination 2001:db8:acad:1::2 G0/0/0
R1(config-if)# exit
```

## Verify DHCPv6 Relay Agent

![[Pasted image 20230930110110.png]]

![[Pasted image 20230930110125.png]]

![[Pasted image 20230930110144.png]]

# ==IX. FHRP Concepts==

- **Alternate default gateway in switched networks where two or more routers are connected to the same VLANs

- **Dynamic method by which these devices can determine the address of a new default gateway**

![[Pasted image 20230930122215.png]]
## Router Redundancy

- **Implement virtual router**
- Two or more routers can **share the same IP address and MAC address**

![[Pasted image 20230930123516.png]]

- IPv4 address of **virtual router is configured as default gateway**
- Redundancy Protocol **determining which router take active role in forwarding traffic**
- **Transition from one forwarding router to another is transparent to end devices**

## Steps for Router Failover

![[Pasted image 20230930124029.png]]
#### - 1. Standby router stops seeing Hello messages from the forwarding router
#### - 2. Standby router assumes the role of the forwarding router
#### - 3. New router assumes both the IPv4 and MAC addresses of the virtual router

## FHRP Options

- **Depending of the equipment's and needs of the network**

![[Pasted image 20230930124347.png]]
## HSRP Overwiew

- **Avoid losing outside network access if default router fails**
- **Cisco-proprietary FRHP protocol
- **Group of devices interfaces, active device route packets and standby device take over when  active device fail

## HSRP Priority and Preemption

- **By default, router with highest IPv4 address <=> active router**

#### Priority

- **Used to determine active router**
- **Default priority <=> 100**
- **Highest HSRP priority <=> active router**
- **Priority Range 0 to 255**

#### Preemption

- **With preemption enabled, router that comes online with higher HSRP priority will be active router**
- **Forces a new election process when the router with preemption comes online**

![[Pasted image 20230930130657.png]]
## HSRP States and Timers

![[Pasted image 20230930131327.png]]

- **Active and standby HSRP routers send hello packets to the HSRP group multicast address every 3 seconds by default**

- **Standby router become active if does not receive hello message from active router after 10 seconds**

- **You can lower timer settings to speed up failover or preemption**

- **In order to avoid increased CPU usage and unnecessary standby state changes, don't set the hello timer below 1 second or the hold timer below 4 seconds.**

- **HSRP active routers remain the active router even if another router with a higher priority joins the network unless preemption is configure on the router that comes up with higher priority and IPv4 addresses**

# ==X. LAN Security Concepts==

## Network Attacks Today

- **Distributed Denial of Service (DDOS)** : Coordinated attack from many devices called zombies with the intention of degrading or halting public access to an organization's website and resources

- **Data Breach** : Steal confidential information

- **Malware** : Attack in which an organization's hosts are infected with malicious software such as WannaCry (encrypt data until ransom is paid)

![[Pasted image 20230930163828.png]]

## Network Security Devices

#### **VPN-Enabled Router**

![[Pasted image 20230930165135.png]]

- Provides a **secure connection to remote users across a public network and into the enterprise-network.**

#### **Next-Generation Firewall (NGFW)**

![[Pasted image 20230930165125.png]]

- **Stateful packet inspection**
- **Application Visibility and Control**
- Next-Generation Intrusion Prevention System **(NGIPS)**
- Advanced malware protection **(AMP)**
- **URL Filtering**
#### **Network Access Control (NAC)**

![[Pasted image 20230930165150.png]]

- Authentification, authorization and accounting (AAA) services
- **Manage Access Policies** across variety of users and device types

*Exemple : Cisco Identity Services Engine (ISE)

## Endpoint Protection

- Endpoint are hosts consist of laptops, desktops, servers and IP phones as well as employee-owned devices (BYODs)

![[Pasted image 20230930165547.png]]

- Email Security Appliance **(ESA)** 
- Web Security Appliance **(WSA)**
- Advanced Malware Protection **(AMP)**
## Cisco Email Security Appliance (ESA)

![[Pasted image 20230930170608.png]]

- Cisco ESA is designed to monitor Simple Mail Transfert Protocol **(SMTP)**
- Constantly updated by real-time from Cisco Talos which **detects and correlates threats and solutions by using worldwide database monitoring system.**

### **Functions**

- **Block known threats**
- Remediate against stealth malware that evaded initial detection
- Discard emails with **bad links** 
- Block access to **newly infected sites**
- **Encrypt content in outgoing email to prevent data loss**

## Cisco Web Security Appliance (WSA)

![[Pasted image 20230930170731.png]]

- Mitigation technology for **web-based threats**
- **Advanced malware protection**
- Application visibility and control, policy controls and reporting
### Functions

- Control **how users access Internet (chat, messaging, video and audio)**
- **Restricted with time and bandwidth limits**
- **Blacklisting of URLs, URL-filtering**
- **Malware Scanning**
- **URL categorization**
- **Encryption and decryption of web traffic**

## Access Control

#### Authentification with a Local Password

- **Weakest and least secure (plaintext)**

```
R1(config)# line vty 0 4
R1(config-line)# password ci5c0
R1(config-line)# login
```

- **SSH is more secure form of remote access**
	- requires **username** and **password** both **encrypted during transmission**
	- More **accountability** because the username is **recorded when a user logs in**

```
R1(config)# ip domain-name example.com
R1(config)# crypto key generate rsa general-keys modulus 2048
R1(config)# username Admin secre Str0ng3rPa55w0rd
R1(config)# ssh version 2 
R1(config)# line vty 0 4
R1(config-line)# transport input ssh
R1(config-line)# login local
```

-  **Local database has limitations**
	- User accounts must be **configured locally on each device** (not applicable in large enterprise)
	- Local database configuration provides no fallback authentification method, no backup method available for authentification, **password recovery is the only option**

==> **A better solution is a central server** in order to have all devices refer to the **same database of usernames and passwords**

## AAA Components

- **Authentification, Authorization and Accounting**
- **Similar to using credit card**
	- Identifies who can use it
	- How much user can spend
	- Keep an account of what items or services the user purchased

![[Pasted image 20230930173935.png]]
## Authentication

- **Local and Server-based** are two common methods of implementing AAA authentification
### Local AAA Authentication

- Ideal for small networks

![[Pasted image 20230930174308.png]]
### Server-Based AAA Authentication

- **AAA server contains the usernames and passwords**
- **Router uses Remote Authentification Dial-In User Service (RADIUS) or Terminal Access Controller Access Control System (TACACS+)**

![[Pasted image 20230930174546.png]]

- **When there are multiple routers and switches => server-based AAA is more appropriate**
## Authorization

- Authorization governs what users **can and connot do on the network after they are authenticated**
- Attributes used by the AAA server **determine privileges and restrictions for that user**

![[Pasted image 20230930195150.png]]
## Accounting

- **Collects and reports usage data** (use by auditing and billing)
- AAA server keeps a **detailed log of exactly what user does**
- **Useful for troubleshooting** or **provides evidence when malicious acts**

![[Pasted image 20230930195232.png]]

## 802.1X

- **Standard port-based access control and authentification protocol**

![[Pasted image 20231002082033.png]]

- **Client (Supplicant) :** : Device running 802.1X-compliant client software available for wired or wireless devices

- **Switch (Authenticator) :** Intermediary between client an authentication server. Identify information from client, verify information with authentication server and relay a response. This could be a wireless access point

- **Authentication server :** Server validate the identity of the client and notifies the switch or wireless access point, client is or is not authorized to access the LAN and switch services.

## Layer 2 Vulnerabilities

- Layer 3 up through Layer 7 used VPNs, firewalls and IPS devices to protect these elements
- However, if Layer 2 is compromised then all the layers above are also affected !

![[Pasted image 20231002082809.png]]
### Layer 2 Attacks

![[Pasted image 20231002083113.png]]

### Switch Attack Mitigation Techniques

![[Pasted image 20231002083201.png]]

- These **Layer 2 solutions will not be effective** if the management **protocols are not secured**
	- **management protocols Syslog**
	- Network Management Protocol **(SNMP)**
	- Trivial File Transfer protocol **(TFTP)**
	- File Transfer Protocol **(FTP)**
	- and others...

- **Strategies** :
	- **secure variants** like **SSH**, Secure Copy Protocol **(SCP)**, Secure FTP **(SFTP)** and Secure Socket Layer/Transport Layer Security **(SSL/TLS)**
	- **out-of-band management network** to manage devices
	- **Use ACLs** to filter unwanted access

## MAC Address Table Flooding

- MAC tables fixed size (switch can run out of ressource to store MAC addresses)
- MAC address flooding attacks =>bombarding the switch with fake source MAC addresses until the table is full

- When the **table is full**, the frame is an **unknown unicast and flood all incoming traffic out all ports on the same VLAN without referencing the MAC table**

![[Pasted image 20231002085156.png]]

- when macof stops, the switch ages out the older MAC address entries and act like a switch again

### MAC Address Table Attack Mitigation

- **Catalyst 6500 switch** can **store 132 000 MAC addresses** in its MAC address table
- **macof** can flood **8000 fake MAC addresses per second**

**Exemple of macof command**

![[Pasted image 20231002085731.png]]

- Not only affect the local switch, also other connected Layer 2 switches when table of a switch is full

### Mitigate MAC address table overflow attack

- **Port Security allow a specified number of source MAC addresses to be learned on the port**

## VLAN Hopping Attacks

- **Enable traffic from one VLAN to be seen by another VLAN without aid of a router
- **Threat actor take advantage of the automatic trunking port feature enabled by default on most switch ports

![[Pasted image 20231002090542.png]]

- threat actor configure the host to spoof 802.1Q signaling and Cisco-proprietary **Dynamic Trunking Protocol (DTP)**

## VLAN Double-Tagging Attack

- **Send Double-tagged 802.1Q frame to the switch

![[Pasted image 20231002091007.png]]

- The frame arrives on the first switch, the first 4-bytes 802.1Q tag is destined for VLAN 10 which is the native VLAN, the switch forwards the packets out all VLAN 10 after remove VLAN 10 tag because native VLAN so not retagged.

![[Pasted image 20231002091051.png]]

- The VLAN 20 tag is still intact and has not been inspected 
- the second switch looks to the tag VLAN 20, **sends the frame on to the target or floods it**

![[Pasted image 20231002092327.png]]

### VLAN Attack Mitigation

- **Disabled trunking on all access ports**
- **Disable auto trunking on trunk links so trunks must be manually enabled**
- **Be sure that the native VLAN is only used for trunk links**

### DHCP Messages

![[Pasted image 20231002093033.png]]

## DHCP Attacks

- **DHCP Starvation**

	- Create a **DoS for connecting clients. Attack tool such as Gobbler**.
	- Gobbler can **see the entire scope of leasable IP addresses and tries to lease them all

- **DHCP Spoofing**

	- **False IP configuration parameters to legitimate clients
	- **Wrong default gateway** : invalid gateway or the IP address of its host to create man-in-the-middle attack. Entirely undetected !
	- **Wrong DNS server** : incorrect DNS server address pointing the user to a nefarious website
	- **Wrong IP address** : invalid IP address creating a DoS attack on the DHCP client

- **Threat Actor Connects Rogue DHCP Server**

	- On the same subnet and VLANs as the target clients, provide clients with false IP configuration information

![[Pasted image 20231002192601.png]]

- **Client Broadcasts DHCP Discovery Messages**

	- Both servers will receive the message and respond

![[Pasted image 20231002192613.png]]

- **Legitimate and Rogue DHCP Reply**

	- **Legitimate DHCP server responds with valid IP configuration parameters
	- **Rogue server responds with a DHCP offer containing IP configuration parameters defined by the threat actor
	- The client will **reply to the first offer received

![[Pasted image 20231002192624.png]]

- **Client Accepts Rogue DHCP Offer**

	- **Rogue offer was received first
	- Client **broadcasts a DHCP request accepting the IP parameters defined by the threat actor
	- Le legitimate and rogue server will receive the request

![[Pasted image 20231002192632.png]]

- **Rogue Server Acknowledges**

	- **Rogue server unicast a reply to the client to acknowledge its request
	- Legitimate server will cease communicating with the client

![[Pasted image 20231002194336.png]]

## ARP Attacks

- **Unsolicited ARP Request** called a **"Gratuitous ARP"**
- When a host sends a gratuitous ARP, **others hosts on the subnet store the MAC address and IPv4 address contained in the gratuitous ARP in their ARP Tables**
- The switch would update its **MAC table accordingly**

- Any **host can claim to be the owner of any IP and MAC address**
- **Threat Actor send a unsolicited ARP Replies with MAC address of the threat actor and the IPv4 address of the default gateway**

#### Tools available on the Internet

- **man-in-the-middle attacks :** dsniff, Cain & Abel, Yersinia and others

**IPv6 uses ICMPv6 Neighbor Discovery Protocol**

-> includes strategies to mitigate Neighbor Advertisement spoofing, similar to IPv6 prevents a spoofed ARP Reply

### Normal State with Converged MAC Tables

![[Pasted image 20231003105204.png]]

### ARP Spoofing Attack

**Attempt to replace R1 as the default gateway**

- Threat inform CC:CC:CC is maps to 10.0.0.1 as the router for the PC1
- Threat inform CC:CC:CC is maps to 10.0.0.11 as the PC1 for the router

![[Pasted image 20231003105254.png]]

### ARP Poisoning Attack with Man-in-the-Middle Attack

- **Remove the correct entry, to add the mac address of the threat and the IP accordingly**
- **ARP Tables are now poisoned**

![[Pasted image 20231003105913.png]]

### Address Spoofing Attack

- **IP addresses and MAC addresses can be spoofed**
- IP address spoofing is when the threat **actor hijacks a valid IP address of another device**
- **Difficult to mitigate**

![[Pasted image 20231003125722.png]]

- The threat actor **create a program or a script to send frames to the switch in order to maintains the incorrect or spoofed information**

- Can be mitigated by **implementing IPSG**

### STP Attack

- **Spoofing the root bridge** and **changing the topology of network**
- Attackers **make their hosts appear as root bridges and capture all traffic in the switched domain

![[Pasted image 20231003130552.png]]

- Attacking host broadcasts STP **bridge protocol data units (BPDUs) with a lower bridge priority to get elected as the root bridge

![[Pasted image 20231003130646.png]]

- **STP attack** is **mitigated by implementing BPDU Guard on all access ports**

### CDP Reconnaissance

- **Cisco Discovery Protocol (CDP)** proprietary Layer 2 link discovery protocol 
- Enable on Cisco devices by default
- CDP **automatically discover other CDP-enabled devices and help auto-configure their connection**
- **Information sent out CDP-enabled ports in a periodic, unencrypted multicast
	- IP address of the device
	- IOS software version
	- platform
	- capabilities
	- native VLAN

- **Information provided by CDP can be used by a threat actor to discover network infrastructure vulnerabilities like Cisco IOS software version used by device**

![[Pasted image 20231003132003.png]]

- To mitigate exploitation of CPD, **limit use of CDP on devices or ports
- **Disable CDP on edge ports that connect to entrusted devices
	- **To disable CDP :** no cdp run
	- **To disable CDP on port :** no cdp enable

- **LLDP (Link Layer Discovery Protocol) are also vulnerable to these reconnaissance attacks**
	- no lldp
	- no lldp transmit
	- no lldp receive

# ==XI. Switch Security Configuration==

- **Layer 2 weakest link in a company's security infrastructure**
## Secure Unused Ports

- **Disable all unused ports** 

```
S1(config)# interface range f0/8 - 24
S1(config-if-range)# shutdown
%LINK-5-CHANGED: Interface FastEthernet0/8, changed state to administratively down
(output omitted)
%LINK-5-CHANGED: Interface FastEthernet0/24, changed state to administratively down
S1(config-if-range)#
```

## Mitigate MAC Address Table Attacks

- Port security **limits the number of valid MAC addresses allowed on a port**
- **Manually configure MAC addresses for a port**

![[Pasted image 20231003140907.png]]

## Enable Port Security

- **Must be configured on access ports or manually configure trunk ports

```
S1(config)# interface f0/1
S1(config-if)# switchport port-security
Command rejected: FastEthernet0/1 is a dynamic port.
S1(config-if)# switchport mode access
S1(config-if)# switchport port-security
S1(config-if)# end
```

**show port-security interface** show the current port security setting for the interface

- **Port Status :** Secure-Down means no devices attached and no violation has occurred 

```
S1# show port-security interface f0/1
Port Security              : Enabled
Port Status                : Secure-down
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 0000.0000.0000:0
Security Violation Count   : 0
S1#
```

- **Port security specifics**

```
S1(config-if)# switchport port-security ?
  aging        Port-security aging commands
  mac-address  Secure mac address
  maximum      Max secure addresses
  violation    Security violation mode
```

## Limit and Learn MAC Addresses

- **Set maximum address learn on port (range 1-8192)**

```
S1(config)# interface f0/1
S1(config-if)# switchport port-security maximum ? 
  <1-8192>  Maximum addresses
```

### **Set static MAC addresses on port**

- **Manually**

```
S1(config)# switchport port-security mac-address mac-address
```

- **Dynamically Learned**

Then **switchport port-security** is entered, current source MAC connected to the port is automatically secured but not added to startup configuration ==> **if the switch is rebooted** ==> port have to **re-learn MAC address

- **Dynamically Learned - Sticky

**MAC address and stick them to the running configuration using this command**

```
S1(config)# switchport port-security mac-address sticky
```

**Saving the running configuration will commit the dynamically learned MAC address to NVRAM

**Example :** Specifies a maximum of 2 MAC addresses, manually configures one secure MAC address and learn max 2 secure MAC address maximum.

```
S1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
S1(config)#
S1(config)# interface fa0/1
S1(config-if)# switchport mode access
S1(config-if)# switchport port-security
S1(config-if)# switchport port-security maximum 2
S1(config-if)# switchport port-security mac-address aaaa.bbbb.1234
S1(config-if)# switchport port-security mac-address sticky
S1(config-if)# end
S1# show port-security interface fa0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 2
Total MAC Addresses        : 2
Configured MAC Addresses   : 1
Sticky MAC Addresses       : 1
Last Source Address:Vlan   : a41f.7272.676a:1
Security Violation Count   : 0
S1# show port-security address
               Secure Mac Address Table
-----------------------------------------------------------------------------
Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                   (mins)    
----    -----------       ----                          -----   -------------
1    a41f.7272.676a    SecureSticky                  Fa0/1        -
1    aaaa.bbbb.1234    SecureConfigured              Fa0/1        -
-----------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 1
Max  Addresses limit in System (excluding one mac per port) : 8192
```

## Port Security Aging

- **Absolute** : secure addresses on the port are deleted after the specified aging time
- **Inactivity** : secure addresses on the port are deleted only if they are inactive for the specified aging time

```
S1(config-if)# switchport port-security aging { static | time time | type {absolute | inactivity}
```

![[Pasted image 20231007165119.png]]

```
S1(config)# interface fa0/1
S1(config-if)# switchport port-security aging time 10 
S1(config-if)# switchport port-security aging type inactivity 
S1(config-if)# end
S1# show port-security interface fa0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Aging Time                 : 10 mins
Aging Type                 : Inactivity
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 2
Total MAC Addresses        : 2
Configured MAC Addresses   : 1
Sticky MAC Addresses       : 1
Last Source Address:Vlan   : a41f.7272.676a:1
Security Violation Count   : 0
```

## Port Security Violation Modes

- If the MAC address of a device attached to the port differs from the list then violation occurs
- By default, port enters the error-disabled state

```
S1(config)# switchport port-security violation { protect | restrict | shutdown }
```

![[Pasted image 20231007165550.png]]

![[Pasted image 20231007165913.png]]

```
S1(config)# interface f0/1
S1(config-if)# switchport port-security violation restrict
S1(config-if)# end
S1#
S1# show port-security interface f0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Restrict
Aging Time                 : 10 mins
Aging Type                 : Inactivity
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 2
Total MAC Addresses        : 2
Configured MAC Addresses   : 1
Sticky MAC Addresses       : 1
Last Source Address:Vlan   : a41f.7272.676a:1
Security Violation Count   : 0
```

## Ports in error-disabled State

- **Port Security Violation in shutdown we can see the interface is down and LED turned off because a non-authorized address is on the port**

```
S1(config)# int fa0/1
S1(config-if)# switchport port-security violation shutdown
S1(config-if)# end
S1#
*Mar  1 00:24:15.599: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to down
*Mar  1 00:24:16.606: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to down
*Mar  1 00:24:19.114: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to up
*Mar  1 00:24:20.121: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
S1#
*Mar  1 00:24:32.829: %PM-4-ERR_DISABLE: psecure-violation error detected on Fa0/1, putting Fa0/1 in err-disable state
*Mar  1 00:24:32.838: %PORT_SECURITY-2-PSECURE_VIOLATION: Security violation occurred, caused by MAC address a41f.7273.018c on port FastEthernet0/1.
*Mar  1 00:24:33.836: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to down
*Mar  1 00:24:34.843: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to down
```

- **Security Violation count is increments by 1**

```
S1# show interface fa0/1 | include down
FastEthernet0/18 is down, line protocol is down (err-disabled)
(output omitted)
S1# show port-security interface fa0/1
Port Security              : Enabled
Port Status                : Secure-shutdown
Violation Mode             : Shutdown
Aging Time                 : 10 mins
Aging Type                 : Inactivity
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 2
Total MAC Addresses        : 2
Configured MAC Addresses   : 1
Sticky MAC Addresses       : 1
Last Source Address:Vlan   : a41f.7273.018c:1
Security Violation Count   : 1
```

Administrator should determine what caused the security violation and re-enable the port with **shutdown** first and then **no shutdown**

```
S1(config)# interface fa0/1
S1(config-if)# shutdown
*Mar  1 00:39:54.981: %LINK-5-CHANGED: Interface FastEthernet0/1, changed state to administratively down
S1(config-if)# no shutdown
*Mar  1 00:40:04.275: %LINK-3-UPDOWN: Interface FastEthernet0/1, changed state to up
*Mar  1 00:40:05.282: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
```

## Verify Port Security

- **Port Security For all Interfaces**

```
S1# show port-security
Secure Port  MaxSecureAddr  CurrentAddr  SecurityViolation  Security Action
                (Count)       (Count)          (Count)
---------------------------------------------------------------------------
      Fa0/1              2            2                  0         Shutdown
---------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 1
Max Addresses limit in System (excluding one mac per port) : 8192
```

- **Port Security for a Specific Interface**

```
S1# show port-security interface fastethernet 0/1
Port Security          	    : Enabled
Port Status            	    : Secure-up
Violation Mode              : Shutdown
Aging Time                  : 10 mins
Aging Type                  : Inactivity
SecureStatic Address Aging  : Disabled
Maximum MAC Addresses       : 2
Total MAC Addresses         : 2
Configured MAC Addresses    : 1
Sticky MAC Addresses        : 1
Last Source Address:Vlan    : a41f.7273.018c:1
Security Violation Count    : 0
```

- **Verify Learned MAC Addresses**

```
S1# show run interface fa0/1
Building configuration...

Current configuration : 365 bytes
!
interface FastEthernet0/1
 switchport mode access
 switchport port-security maximum 2
 switchport port-security mac-address sticky
 switchport port-security mac-address sticky a41f.7272.676a
 switchport port-security mac-address aaaa.bbbb.1234
 switchport port-security aging time 10
 switchport port-security aging type inactivity
 switchport port-security
end
```

- **Verify Secure MAC Addresses**

```
S1# show port-security address
               Secure Mac Address Table
-----------------------------------------------------------------------------
Vlan    Mac Address       Type                          Ports   Remaining Age
                                                                   (mins)
----    -----------       ----                          -----   -------------
   1    a41f.7272.676a    SecureSticky                  Fa0/1        -
   1    aaaa.bbbb.1234    SecureConfigured              Fa0/1        -
-----------------------------------------------------------------------------
Total Addresses in System (excluding one mac per port)     : 1
Max Addresses limit in System (excluding one mac per port) : 8192
```

## Mitigate VLAN Attacks

***VLAN hopping in 3 Ways :

- **Spoofing DTP messages** to enter trunking mode
- Introducing a **rogue switch and enabling trunking, access all the VLANs on the victim switch from the rogue switch
- **Double-tagging** attack

1. **Disable DTP (auto trunking) negotiations on non-trunking ports** with switchport mode access

2. **Disable unused ports and put them in an unused VLAN**

3. **Manually enable the trunk link on a trunking port** with switchport mode trunk

4. **Disable DTP (auto trunking) negotiations on trunking ports** with switchport nonegotiate

5. **Set the native VLAN to VLAN other than VLAN 1** with switchport trunk native vlan *vlan_number*

In this example : fa0/1 - 16 are active access ports, fa0/17 - 20 not used, f0/21 - 24 are trunk ports 

```
S1(config)# interface range fa0/1 - 16
S1(config-if-range)# switchport mode access 
S1(config-if-range)# exit
S1(config)# interface range fa0/17 - 20
S1(config-if-range)# switchport mode access
S1(config-if-range)# switchport access vlan 1000
S1(config-if-range)# shutdown 
S1(config-if-range)# exit
S1(config)# interface range f0/21 - 24
S1(config)# switchport mode trunk
S1(config)# switchport nonegotiate
S1(config)# switchport trunk native vlan 999
S1(config)#
```

## Mitigate DHCP Attacks

- **DHCP starvation attack** is to create **Denial of Service (DoS) for connecting clients
- **Attack tool such as Gobbler**
- **DHCP spoofing attacks can be mitigated with DHCP snooping on trusted ports

### DHCP Snooping

- **Don't rely on source MAC addresses
- **DHCP Snooping** determines whether **DHCP messages are form administratively configured trusted or untrusted source**
- **Devices under administrative control** like **switches, routers and servers are trusted sources
- **Device beyond the firewall or outside the network is an untrusted source

![[Pasted image 20231007182230.png]]

 - **DHCP snooping binding table includes the source MAC address and IP address of on a device on an untrusted port

### Steps to Implement DHCP Snooping

**1. Enable DHCP snooping with ==>**  ip dhcp snooping 
**2. On trusted ports ==>** ip dhcp snooping trust 
**3. Limit the number of DHCP discovery messages that can be received per second on untrusted ports ==>** ip dhcp snooping limit rate
**4. Enable DHCP snooping by VLAN or by a range of VLANs** ip dhcp snooping *vlan*

![[Pasted image 20231007182911.png]]

```
S1(config)# ip dhcp snooping
S1(config)# int f0/1
S1(config-if)# ip dhcp snooping trust
S1(config-if)# exit
S1(config)# int range f0/5 - 24
S1(config-if-range)# ip dhcp snooping limit rate 6
S1(config-if-range)# exit
S1(config)# ip dhcp snooping vlan 5,10,50-52
S1(config)# end
```

```
S1# show ip dhcp snooping
Switch DHCP snooping is enabled
DHCP snooping is configured on following VLANs:
5,10,50-52
DHCP snooping is operational on following VLANs:
none
DHCP snooping is configured on the following L3 Interfaces:
Insertion of option 82 is enabled
   circuit-id default format: vlan-mod-port
   remote-id: 0cd9.96d2.3f80 (MAC)
Option 82 on untrusted port is not allowed
Verification of hwaddr field is enabled
Verification of giaddr field is enabled
DHCP snooping trust/rate is configured on the following Interfaces:
Interface                  Trusted    Allow option    Rate limit (pps)
-----------------------    -------    ------------    ----------------   
FastEthernet0/1            yes        yes             unlimited
  Custom circuit-ids:
FastEthernet0/5            no         no              6         
  Custom circuit-ids:
FastEthernet0/6            no         no              6         
  Custom circuit-ids:
S1# show ip dhcp snooping binding
MacAddress         IpAddress       Lease(sec) Type          VLAN Interface
------------------ --------------- ---------- ------------- ---- --------------------
00:03:47:B5:9F:AD  192.168.10.11   193185     dhcp-snooping 5    FastEthernet0/5
```

## Mitigate ARP Attacks

- **ARP Attack :** Unsolicited ARP requests to other hosts on the subnet with the MAC Address of the threat actor and the IP address of the default gateway

- #### **Dynamic ARP inspection (DAI)** requires DHCP snooping and prevent ARP attacks by :

	- **Not relaying invalid or gratuitous ARP Requests out to other ports in the same VLAN
	- **Intercepting all ARP Requests and Replies on untrusted ports
	- Verifying each intercepted packets for a **valid IP-to-MAC binding
	- **Dropping** and **logging ARP Requests** coming **from invalid sources to prevent ARP poisoning**
	- **Error-disabling the interface if the configured DAI number of ARP packets is exceeded

#### DAI Implementation Guidelines

- **Enable DHCP snooping globally**
- **Enable DHCP snooping on selected VLANs
- **Enable DAI on selected VLANs
- **Configure trusted interfaces for DHCP snooping and ARP inspection**

![[Pasted image 20231009074430.png]]

- **DAI requires DHCP snooping binding table to operate

```
S1(config)# ip dhcp snooping
S1(config)# ip dhcp snooping vlan 10
S1(config)# ip arp inspection vlan 10
S1(config)# interface fa0/24
S1(config-if)# ip dhcp snooping trust
S1(config-if)# ip arp inspection trust
```

**DAI check for both destination or source MAC and IP addresses

- **Destination MAC** : checks destination MAC address in the Ethernet header against the target MAC address in ARP body
- **Source MAC :** checks source MAC address in the Ethernet header against the sender MAC address in the ARP body
- **IP address :** checks for invalid or unexpected IP addresses including addresses 0.0.0.0, 255.255.255.255 and all IP multicast addresses

```
S1(config)# ip arp inspection validate ?
  dst-mac  Validate destination MAC address
  ip       Validate IP addresses
  src-mac  Validate source MAC address
S1(config)# ip arp inspection validate src-mac
S1(config)# ip arp inspection validate dst-mac
S1(config)# ip arp inspection validate ip
S1(config)# do show run | include validate
ip arp inspection validate ip 
S1(config)# ip arp inspection validate src-mac dst-mac ip
S1(config)# do show run | include validate
ip arp inspection validate src-mac dst-mac ip
```

## Mitigate STP Attacks

#### PortFast and BPDU Guard

- **Manipulate Spanning Tree Protocol (STP)** by spoofing the root bridge and changing the topology of a network
- **Use of PortFast and Bridge Protocol Data Unit Guard (BPDU) to mitigate**

![[Pasted image 20231009080846.png]]

- **PortFast** : Brings interface configured as an access port to the forwarding state from a blocking state, bypassing the listening and learning states. Apply to end-user ports. 

- **BPDU Guard** : Immediately error disables a port that receives a BPDU. Like a portfast, BPDU guard should only be configured on interfaces attached to end devices
#### Configure PortFast

- **PortFast bypasses the STP listening and learning states to minimize the time that access ports must wait for STP to converge

- **If PortFast is enabled on a port connecting to another switch there is a risk of creating spanning-tree loop

```
S1(config)# interface fa0/1
S1(config-if)# switchport mode access
S1(config-if)# spanning-tree portfast
%Warning: portfast should only be enabled on ports connected to a single
 host. Connecting hubs, concentrators, switches, bridges, etc... to this
 interface when portfast is enabled, can cause temporary bridging loops.
 Use with CAUTION
%Portfast has been configured on FastEthernet0/1 but will only
 have effect when the interface is in a non-trunking mode.
S1(config-if)# exit
S1(config)# spanning-tree portfast default
%Warning: this command enables portfast by default on all interfaces. You
 should now disable portfast explicitly on switched ports leading to hubs,
 switches and bridges as they may create temporary bridging loops.
S1(config)# exit
S1# show running-config | begin span
spanning-tree mode pvst
spanning-tree portfast default
spanning-tree extend system-id
!
interface FastEthernet0/1
 switchport mode access
 spanning-tree portfast
!
interface FastEthernet0/2
!
interface FastEthernet0/3
!
interface FastEthernet0/4
!
interface FastEthernet0/5
! 
(output omitted)
```

## Configure BPDU Guard

- Even though PortFast is enabled, interface listen BPDUs
- If any BPDUs are received on a BPDU Guard enabled port then the port is put into error-disabled state
- Must be manually re-enabled or automatically recovered through the **errdisable recovery cause bpduguard**
- BPDU Guard can be enabled on a port by using **spanning-tree bpduguard enable**
- **snapping-tree portfast bpduguard default** enable BPDU guard on all PortFast-enabled ports

```
S1(config)# interface fa0/1
S1(config-if)# spanning-tree bpduguard enable
S1(config-if)# exit
S1(config)# spanning-tree portfast bpduguard default
S1(config)# end
S1# show spanning-tree summary
Switch is in pvst mode
Root bridge for: none
Extended system ID           is enabled
Portfast Default             is enabled
PortFast BPDU Guard Default  is enabled
Portfast BPDU Filter Default is disabled
Loopguard Default            is disabled
EtherChannel misconfig guard is enabled
UplinkFast                   is disabled
BackboneFast                 is disabled
Configured Pathcost method used is short
(output omitted)
```

# ==XII. WLAN Concepts==

### Benefits of Wireless (WLAN)

- **Mobility possible
- **Cost savings
- **Temporary locations
- **Adapt rapidly
### Types of Wireless Networks

Wireless networks based on **Institute of Electrical and Electronics Engineers (IEEE) Standards**, classified into four main types !

- **Wireless Personal-Area Networks (WPAN)

	- **Low powered transmitters for a short-range network (6 to meters)
	- **Bluetooth and ZigBee based device ==> used in WPANs
	- **WPANs based on 802.15 standard and 2.4 GHz radio frequency**

![[Pasted image 20231009102537.png]]

- ## **Wireless LANs (WLAN)**

	- **Transmitters to cover medium-sized network (up to 90 meters)
	- **Based on 802.11 standard and use 2.4 GHz or 5 GHz radio frequency**

![[Pasted image 20231009102801.png]]

- ## **Wireless MANs (WMAN)

	- **Transmitters to provide wireless service over a larger geographic area
	- **WMAN suitable for providing wireless access** to a **metropolitan city** or **specific district**
	- Use **specific licensed frequencies**

![[Pasted image 20231009103608.png]]

- ## **Wireless Wide-Area Networks (WWANs)

	- Transmitters to provide **coverage over an extensive geographic area**
	- **Suitable for national and global communications
	- Use **specific licensed frequencies**

![[Pasted image 20231009103804.png]]
## Wireless Technologies

- **Uses unlicensed radio spectrum to send and receive data
- **Unlicensed spectrum is accessible to anyone who has a wireless router and wireless technology

## Bluetooth

- An **IEEE 802.15 WPAN standard** that uses a **device-pairing process to communicate over distances up to (100 m)

- **Found** in **smart home devices**, **audio connections**, **automobiles** and other devices with **short distance connection**

**Two types of Bluetooth radios :**

- **Bluetooth Low Energy (BLE) :** Supports multiple network technology **including mesh topology to large scale network devices**
- **Bluetooth Basic Rate/Enchanced Rate (BR/EDR) :** Supports **point to point topologies** and is **optimized for audio streaming

## WiMAX (Worldwide Interoperability for Microwave Access)

- **Alternative to broadband wired internet connections** competing with DSL and cable
- **Used in areas that are not yet connected to a DSL or cable provider
- **IEEE 802.16 WWAN standard that provides high-speed wireless broadband access up to 50 km

![[Pasted image 20231009104928.png]]

- **Operates similar way to Wifi** but at **higher speeds** and over **greater distances** and for a **greater number of users**
- **Uses a network of WiMAX towers similar to cell phone towers**
- **WiMAX may share space on the same tower (as shown in the figure)**

## Cellular Broadband

- **Cellular 4G/5G wireless mobile networks** primarily **used cellular phones** but can be used in **automobiles, tablets** and **laptops**
- **Multi-Access Networks carrying both data and voice communications**
- **Cell site => cellular tower transmitting signals in a given area
- **2 Types of cellular network :** Global System for Mobile (**GSM**) and Code Division Multiple Access (**CDMA**)

![[Pasted image 20231009105202.png]]

- **GSM internationally recognized**
- **CDMA primarily used in the US**

- **4th Generation mobile network (4G)** is the current mobile network ==> 4G delivers **speeds that are 10 times the previous 3G networks** ==> **4G promise delivering 100 times faster speeds than 4G

## Satellite Broadband

- **Network access to remote sites**
- **Need of satellite dish aligned with specific geostationary Earth orbit satellite

![[Pasted image 20231010172820.png]]

- **More expensive, requires clear line of sight
- **Used by rural homeowners and business where cable and DSL are not available
## 802.11 Standards

**IEEE 802.11 standard developed over the years

- Standards define **how radio frequencies are used for wireless links** 

- Standards define **if wireless device have one antenna to transmit and receive wireless signals on specified radio frequency (2.4 GHz or 5 GHz) 

![[Pasted image 20231010173200.png]]

- Newer standards **transmit and receive at higher speeds that require access points (APs) and wireless client use multiple antennas (improve performance) using the multiple-input and multiple-output (MIMO)**

- **Eight transmit and receive antennas** to **increase throughput

## Radio Frequencies

**Wireless device use radio waves range** of the **electromagnetic spectrum**

- **2.4 GHz (UHF) - 802.11b/g/n/ax 
- **5 GHz (SHF) - 802.11a/n/ac/ax

#### Electromagnetic Spectrum

![[Pasted image 20231010175900.png]]

## Wireless Standards Organizations

**3 Organizations influencing WLAN standards

- **ITU : International Telecommunication Union** regulates allocation of the radio frequency spectrum and satellite orbits through the ITU-R (Radiocommunication Sector)

![[Pasted image 20231010180411.png]]


- **IEEE :** **how a radio frequency is modulated to carry information**, maintains also the standards for LAN/MAN with 802.3 Ethernet and 802.11 WLAN.

![[Pasted image 20231010180617.png]]

- **Wi-Fi Alliance :** **Non-Profit industry trade association** devoted to promoting **growth and acceptances of WLANs**

**Association of vendors to improve the interoperability of products based on 802.11 standard** for conformance to industry **norms and adherence to standards.**

![[Pasted image 20231010180745.png]]

## WLAN Components

Wireless deployments **needs two devices**, **a radio transmitter** and **a radio receiver tuned to the same frequencies

- **End devices with wireless NICs
- **Network device with wireless router or wireless AP

**USB wireless adapter can be used** if we don't have wireless NICs

![[Pasted image 20231010183100.png]]

### **Wireless router serves as :**

- **Access point** : provides 802.11a/b/g/n/ac wireless access
- **Switch** : provides four-port, full duplex, 10/100/1000 Ethernet switch to interconnect wired devices
- **Router** : provides a default gateway for connecting to other network infrastructures such as internet

Wireless router advertises its wireless services by sending beacons with **'shared service set identifier' (SSID)**

- **Devices who discover the SSID attempt to associate and authenticate** 

**Wireless Routers** can also :

- **High-speed access
- **Support for video streaming
- **IPv6 addressing
- **Quality of Service** (**QoS**)
- **Configuration utilities**
- **USB ports to connect printers or portables drives

![[Pasted image 20231010183719.png]]

Home users who **want to extend their network services can implement Wi-Fi range extenders
=> implement Wi-Fi range extenders

### Wireless Access Points

- **Best solution** is **install another wireless access point** to **provide dedicated wireless access** to the user devices

![[Pasted image 20231010183918.png]]

- **Wireless client use** their **wireless NIC** to **discover** nearby **APs advertising their SSID**
- Clients attempt to **associate and authenticate with an AP**

## AP Categories

- **Autonomous APs** : Configure using CLI or GUI, useful with only couple of APs, require manual configuration and management (can become overwhelming)

![[Pasted image 20231010184827.png]]

- **Controller-based APs :** no initial configuration, also called **lightweight APs (LAPs)**, use of **Lightweight Access Point** Protocol to communication with **WLAN controller (WLC)**. Useful if many APs require in the network, automatically configured and managed by WLC.

![[Pasted image 20231010185132.png]]

- **Link Aggregation Group (LAG)** must be configure to **provides redundancy** and **load-balancing**, we can't use **Port Aggregation Protocol (PaGP)** or **Link Aggregation Control Protocol (LACP)**

## Wireless Antennas

**Most business class APs require external antennas** 

- **Omnidirectional Antennas** : Omnidirectional antennas provide 360-degree coverage,  ideal in houses, open office areas, conference rooms and outside areas

![[Pasted image 20231010223849.png]]

- **Directional Antennas** : Focus the radio signal in a given direction. Enhances the signal to and from the AP in the direction the antenna is pointing. Provides stronger signal strength in one direction, reduced signal strength in all other directions. Example of directional Wi-Fi antennas are Yagi and parabolic dish antennas.

![[Pasted image 20231010223947.png]]

- **MIMO Antennas** : Multiple Input Multiple Output (MIMO) uses multiple antennas to increase available bandwidth for IEEE 802.11n/ac/ax wireless networks. Up to eight transmit and receive antennas can be used to increase throughput.

![[Pasted image 20231010223957.png]]

## 801.11 Wireless Topology Modes

**3 Wireless Topology modes, Ad hoc mode and Infrastructure mode are the main used**

- ### **Ad hoc mode** :

**Connect in peer-to-peer (P2P)** without APs or wireless routers

![[Pasted image 20231011143306.png]]

Ad hoc network => **Independent Basic Service Set (IBSS)

**Example :** Bluetooth, Wi-Fi Direct

- ### **Infrastructure mode :**

**Wireless clients interconnect via wireless router or AP (WLANs)

![[Pasted image 20231011143518.png]]

APs connect to the network infrastructure using wired distribution system (Ethernet cable)

- **Tethering** :

Phone or Table with cellular data access enabled a personal hotspot

![[Pasted image 20231011143813.png]]

Temporary Quick Solution to provides wireless services of a Wi-Fi router

## BSS and ESS

*Two topology building blocks : A **Basic Service Set (BSS)** and an **Extended Service Set (ESS)**

#### **Basic Service Set (BSS):** Single AP interconnecting all associated wireless clients

In this figure, circles depict the coverage are for the BSS which is **Basic Service Area (BSA)

![[Pasted image 20231011144304.png]]

When the **client moves out of its BSA**, it can **no longer directly communicate with other wireless clients within the BSA**

MAC address of the AP is used to uniquely identify each BSS which is called **Basic Service Set Identifier** 

**BSSID** ==> formal name of the BSS associated with only one AP.

#### Extended Service Set (ESS) : 

- **ESS** is the union of two or more BSSs interconnect by a wired DS
- **Each ESS have a SSID** and **each BSS have a BSSID**

![[Pasted image 20231011154450.png]]

- **Wireless client in one BSA** can **communicate with wireless client in another BSA within the same ESS**

- Wireless client can move from one BSA to another

## 802.11 Frame Structure

![[Pasted image 20231011155027.png]]

### **All 802.11 wireless frames contain the following fields**

- **Frame Control** : Identifies the type of wireless frame and contains subfields for protocol version, frame type, address type, power management and security-settings
- **Duration** : Indicate the remaining duration needed to receive the next frame transmission

### **From a wireless device**

- **Address 1 Receiver Address :** MAC address of the AP
- **Address 2 Transmitter Address :** MAC address of the sender
- **Addresses 3 SA/DA/BSSID :** MAC address of the destination which could be a wireless device or wired device

### **From the AP**

- **Address 1 Receiver Address** : MAC address of the sender
- **Address 2 Transmitter Address :** MAC address of the AP
- **Address 3 SA/DA/BSSID :** MAC address of the wireless destination
- **Sequence Control :** information to control sequencing and fragmented frames
- **Address4 :** usually missing because it is used only in ad hoc mode
- **Payload :** data for transmission 
- **FCS :** Layer 2 error control

## CSMA/CA

**WLANs are half-duplex** (one client cant transmit or receive at any given moment) but **Wireless client cannot hear while sending**

- **Carrier Sense Multiple Access with Collision Avoidance (CSMA/CA)** resolve this problem, determine how and when to send data on the network

**Wireless Client does :**

1. **Listens** to the channel to see **if it is idle (no traffic on the channel** (channel also called the carrier)) 
2. **Sends a Request To Send (RTS) message to the AP** to request **dedicated access to the network**
3. **Receive a Clear To Send (CTS) message form the AP** granting access to send
4. If does **not receive a CTS message**, wait a random amount of time before restarting the process
5. **After receives CTS, transmit data**
6. **All transmissions are acknowledged**, **if Client does not receive an acknowledgment ==> collision occurred and restart process


## Wireless Client and AP Association

- Associate with an AP or Wireless router (important part of the 802.11 process)
- Discovering a WLAN and subsequently connecting to it

![[Pasted image 20231011160756.png]]
### **3 Stages** :

- **Discover a Wireless AP**
- **Authenticate with AP**
- **Associate with AP**

**Wireless Clients and AP** must agree on specific parameters (parameters must be configured on the AP)

- **SSID** : **SSID name appears in the list of available wireless networks on a client.** In larger organizations, each SSID is mapped to one VLAN. **Depending on the network configuration**, several **APs on a network can share a common SSID**

- **Password :** required from the wireless client to **authenticate to the AP**

- **Network Mode** : refers to the **802.11a/b/g/n/ac/ad WLAN standards**, AP and Wireless routers **can operate in a Mixed mode** (support clients connecting via multiple standards)

- **Security mode** : WEP, WPA or WPA2. Always enable the highest security level supported

- **Channel settings** : **frequency bands used to transmit** wireless data. Wireless routers, APs can **scan the radio frequency channels** ==> **automatically select an appropriate channel** setting. **Channel** can be **set manually if there is interference with another AP, Wireless device.

## Passive and Active Discover Mode

Wireless Clients connect to the AP using a scanning (probing) process (can be passive or active)

### Passive Mode

- **AP openly advertises** with **broadcast beacon frames** with **SSID, supported standards and security settings**
- **Wireless Client can now choose which network and AP to use**

![[Pasted image 20231011162426.png]]
## Active Mode

- Wireless clients **must know the name of the SSID**
- Wireless clients **initiate with broadcasting a probe request frame on multiple channels (with SSID name, standard supported)**

![[Pasted image 20231011162631.png]]

- **APs configured with the SSID** ==> **send probe response (with SSID, supported standards, security settings
- **Wireless client can also** ==> send **probe request without SSID name to discover WLAN networks**

- **APs configured** to **broadcast beacon frames** would **respond to the wireless client (with probe response and SSID name)
- APs configure **with broadcast SSID feature disable do not respond 

## CAPWAP Operation

- **CAPWAP** ==> **IEEE standard protocol** ==> **enable WLC to manage multiple APs and WLANs

- **CAPWAP** ==> **responsible for encapsulation and forwarding of WLAN client traffic between an AP and a WLC**

- **CAPWAP** ==> **based on LWAPP** but **adds additional security** with **Datagram Transport Layer Security (DTLS)**

![[Pasted image 20231011163621.png]]

- **CAPWAP** ==> **Establishes tunnels** on **User Datagram Protocol (UDP) ports** ==> **operate on IPv4 and IPv6** (*IPv4 uses by default)

- **IPv4** and **IPv6 use UDP ports 5246 and 5247** ==> **Port 5246 control messages** *used by the WLC to manage AP*, **Port 5247**  *is used to encapsulate data packets traveling to and from wireless clients

- **CAPWAP tunnels** use **different IP protocols**, IPv4 uses IP protocol 17 and IPv6 uses IP protocol 136

## Split MAC Architecture

**CAPWAP split MAC concept** :

- **AP MAC Functions**
- **WLC MAC Functions** 

![[Pasted image 20231011165801.png]]

## DTLS Encryption

- **DTLS protocol** ==> **security between the AP** and **the WLC
- **Communicate using encryption** and **prevents eavesdropping or tampering**

![[Pasted image 20231011170038.png]]

- **DTLS enabled by default** to **secure CAPWAP control channel** but **disabled by default for data channel**

- **CAPWAP management** and **control traffic exchanged** **between AP and WLC** is **encrypted and secured** by **default** (Man-In-the-Middle(MITM Attacks))

- **CAPWAP data encryption is optional and enabled per AP** (require DTLS licence install on WLC prior to being enabled on an AP)

- When enabled, **WLAN client traffic is encrypted at the AP being forwarded to the WLC and vice versa**

## FlexConnect APs

- **Wireless Solution for branch office and remote office deployments**
- Lets you **configure and control access points in a branch office from the corporate office through a WAN link** (without deploying a controller in each office)

![[Pasted image 20231011170833.png]]
### Two modes of operation for FlexConnect AP

- **Connected mode :** **WLC is reachable**, **send traffic through CAPWAP tunnel**, ***WLC performs all its CAPWAP functions** (as shown in the figure)

- **Standalone mode :** **WLC is unreachable**, **FlexConnect has lost / failed to establish CAPWAP connectivity with WLC**, a ***FlexConnect AP assume some of the WLC functions** (switching client data traffic locally, performing client authentication locally)

## Frequency Channel Saturation

Wireless LAN devices have **transmitters and receivers tuned to specific frequencies of radio waves to communicate**

- **Common practice ==> frequencies allocated as ranges** ==> **ranges split into ranges called channels**

- If **demand for a specific channel is too high** ==> **channel become oversaturated**

- **Saturation of the wireless medium** ==> **degrades quality of the communication**

*Somes techniques have been created ==> improve wireless communication and alleviate saturation ==> mitigate channel saturation by using channels in a more efficient way

## Direct-Sequence Spread Spectrum (DSSS)

- **Modulation technique** designated to **spread a signal over a larger frequency band
- **Spread spectrum techniques were developed during war time** to make it *more difficult for enemies to intercept or jam a communication signal

![[Pasted image 20231011173236.png]]

- **Spreading the signal over a wider frequency** which effectively **hides the discernable peak of the signal**
- **Properly configured receiver** can **reverse DSSS modulation** and **re-construct the original signal**
- **Used by 802.11b devices to avoid interference** from other device **using the same 2.4GHz frequency

## Frequency-Hopping Spread Spectrum (FHSS)

- **Spread spectrum methods** to communicate
- **Transmit radio signals** by rapidly **switching a carrier signal among many frequency channels**

![[Pasted image 20231011174546.png]]

- **Sender** and **receiver** must **be synchronize** to "know" which channel to jump to.
- **Channel hopping process allows for a more efficient usage of the channels, decreasing channel congestion
- FHSS **used by the original 802.11 standard** ==> **Walkie-talkies and 900 MHz cordless phones use FHSS** and **Bluetooth** uses a variation of **FHSS**

## Orthogonal Frequency-Division Multiplexing (OFDM)

*This is a subset of frequency-Division Multiplexing (OFDM)

- **Single channel** uses **multiple sub-channels on adjacent frequencies**
- **Sub-channels** in an OFDM system are **precisely orthogonal to another** which **allow the sub-channels to overlap without interfering**

![[Pasted image 20231011175753.png]]

- **OFDM** is **used by a number of communication systems including 802.11a/g/n/ac**
- **New 802.11ax uses a variation of OFDM** called **Orthogonal frequency-division multiaccess (OFDMA)

## Channel Selection

WLANs **requiring multiple APs to use non-overlapping channels**

- **802.11b/g/n standards operate** in the **2.4GHz to 2.5 GHz spectrum**

- **2.4 GHz subdivided into multiple channels**

- **Each channel is allotted 22 MHz bandwidth and separated from the next channel by 5MHz** 

![[Pasted image 20231011180852.png]]

- **802.11b standard identifies 11 channels for North America** (13 in Europe and 14 in Japan)

- **Interference occurs when one signal overlaps a channel reserved for another signal ==> causing possible distortion 

**Best practice for 2.4 GHz WLANs require multiple APs** to use **non-overlapping channels** (modern APs will do this automatically)

If there are **three adjacent APs, use channels 1, 6 and 11** !

![[Pasted image 20231011214129.png]]

### For 5GHz Standards 802.11a/n/ac

- **24 channels
- **Divided into three sections
- **Each channel separated by 20 MHz
- We can see all **24 Unlicensed National Information Infrastructure (U-NNI)**
- Although there is a **slight overlap at the tails of each channel's frequency**
- **5 GHz wireless can provide faster data transmission** (large amount of non-overlapping wireless channels)

### 5 GHz First Eight Non-Interfering Channels

![[Pasted image 20231011214147.png]]
### 5 GHz Non-Interfering Channels for 802.11a/n/ac

![[Pasted image 20231011215055.png]]

## Plan a WLAN Deployment

- **Number of users supported by a WLAN depends** on :
	- the **geographical layout of the facility** (number of bodies and devices that can fit in a space)
	- **Data rates users expect**
	- **Use of non-overlapping channels** by multiple APs **in an ESS**
	- **Transmit power settings**

**When planning the location of APs** ==> **circular coverage area is important** ==> but **there are some additional recommendations :**

![[Pasted image 20231011215428.png]]

- If **APs are to use existing wiring**
- If there are **locations where APs cannot be placed**
- Notes **potential sources of interference** (microwave ovens, wireless video cameras, fluorescent lights, motion detectors or any device that use 2.4 GHz range)
- Position **APs above obstructions**
- Position **APs vertically near the ceiling in the center of each coverage area**
- Position APs **in locations where users are expected to be** (conference rooms)
- **If an IEEE 802.11 network has been configured** for mixed mode ==> **clients may experience slower than normal speeds** in order to **support the older wireless standards**

*Always consult the specifications for the AP when planning for coverage areas => depending on the WLAN standard or mix of standards, nature of the facility, transmit power of the AP*

## Wireless Security Overview

- **Interception of data** : Wireless data should be encrypted to prevent if from being read by eavesdroppers

- **Wireless intruders** : Unauthorized users attempting to access network resources can be deterred through effective authentication techniques

- **Denial of Service (DoS) Attacks** : Access to WLAN services can be compromised either accidentally or maliciously. 

- **Rogue APs** : Unauthorized APs installed by a well-intentioned user or for malicious purposes can be detected using management software

## DoS Attacks

*Wireless DoS attacks can be the result of :*

- **Improperly configured devices** : Configuration errors ==> intruder with administrator privileges could disable a WLAN

- **Malicious user intentionally interfering with the wireless communication** : Disable the wireless network completely, no legitimate device can access the medium

- **Accidental interference** : Interference from other wireless devices (microwave ovens, cordless phones, baby monitors...) ==> 2.4 GHz is more prone to interference than 5 GHz band

![[Pasted image 20231011222748.png]]

**Minimize risk of DoS attacks :**

- **Harden all devices
- **Keep passwords secure
- **Create backups
- **Configuration changes incorporated off-hours
- **Monitor the WLAN ==> for interference problems
- **5 GHz should be used in areas prone to interference

## Rogue Access Points 

- **AP** or **wireless router connected to corporate network without explicit authorization** (against corporate policy)

- **Rogue AP** can **capture MAC addresses**, **data packets**, **gain access to network ressources**, **launch man-in-the-middle attack**

- **Personal network hotspot** can be **used as rogue AP**

*To prevent installation of rogue APs => WLCs with rogue AP policies must be configured and use of monitoring software to actively monitor the radio spectrum for unauthorized APs*

![[Pasted image 20231011231724.png]]

## Man-in-the-Middle Attack

- Positioned between two legitimate entities (read or modify data)
- Way to create MITM attack => 'evil twin AP' => introduce rogue AP with same SSID as legitimate AP

![[Pasted image 20231011231747.png]]

- Locations offering free Wi-Fi such as airports, cafes, restaurants ==> popular spots for this attack
- Client near rogue AP will have a stronger signal and likely associate with it ==> steal user's passwords, personal information, gain access and compromise system

![[Pasted image 20231011232113.png]]

*To prevent MITM ==> identifying legitimate devices on the WLAN => users must be authenticated ==> monitored for abnormal devices or traffic*

## SSID Cloaking and MAC Address Filtering

*Wireless signal can travel through solid matter like ceilings, floors, walls, outside of the home or office space*

**Prevent wireless intruders** :

- **SSID Cloaking**

Clients must manually configure SSID to connect to the network

![[Pasted image 20231011232654.png]]

- **MAC Addresses Filtering**

**Manually permit** or **deny clients wireless access based** on their physical **MAC address**

![[Pasted image 20231011233031.png]]

Devices with different MAC addresses will **not be able to join the 2.4 GHz WLAN**

## 802.11 Original Authentication Methods

**SSID** are **easily discovered** even if **APs do not broadcast** them and **MAC address can be spoofed**

Best way to secure wireless networks is to use **authentication** and **encryption systems**

### Two types of authentication introduced with original 802.11 standard

- **Open system authentication (must use VPN so authentication and encryption)
- **Shared key authentication (authenticate and encrypt data)

![[Pasted image 20231012095122.png]]

## Shared Key Authentication Methods

![[Pasted image 20231012095210.png]]

## Authenticating a Home User

- **Personal** : use pre-shared key (PSK)
- **Enterprise** : need **Remote Authentication Dial-In User Service (RADIUS)** authentication server (more complicated to set up) provides additional security ! users must authenticate **using 802.1X standard** ==> uses **Extensible Authentication Protocol (EAP)**

![[Pasted image 20231012100728.png]]

## Encryption Methods

- **Temporal Key Integrity Protocol (TKIP) :**  used by **WPA**, **use of WEB** but **encrypts the Layer 2 payload using TKIP** ==> carries out a **Message Integrity Check (MIC)** to ensure message has not **been altered**

- **Advanced Encryption Standard (AES) :** used by **WPA2** ==> **far stronger** ==> **Counter Cipher Mode with Block Chaining Message Authentication Code Protocol (CCMP)** ==> **allows destination hosts** to **recognize** if **encrypted** and **non-encrypted bits have been altered**

![[Pasted image 20231012100744.png]]
## Authentication in the Enterprise

*Used of Authentication, Authorization and Accounting (AAA) RADIUS server

- **RADIUS Server IP address** : reachable address of the RADIUS server

- **UDP port numbers** : UDP ports 1812 for RADIUS Authentication, 1813 for RADIUS Accounting (can also operate using UDP ports 1645 and 1646)

- **Shared key** : used to authenticate the AP with the RADIUS server


![[Pasted image 20231012102025.png]]

- **Shared key** must be **configured on AP** to **authenticate with RADIUS server**

- **User authentication** and **authorization handled by 802.1X standard** ==> **centralized server-based authentication of end users** ==> **uses EAP** to **communicate with the AP and RADIUS server** ==> **secure authentication mechanism** and **negotiate a secure private key** for wireless **encryption session** ==> using **TKIP** or **EAS encryption**

## WPA3

*WPA3 not readily available => WPA2 no longer considered secure*
### WPA3 Features

- **WPA3-Personal** : In WPA2-Personal, the threat actors can listen "handshake" between wireless client and the AP ==> use brute force attack to guess PSK

*Remediation : WPA3-Personal disable this attack with Simultaneous Authentication of Equals (SAE) => PSK never exposed*


- **WPA3-Enterprise** : use of 192-bit cryptographic suite, adheres to the Commercial National Security Algorithm (CNSA) Suite ==> commonly used in high security Wi-Fi networks


- **Open Networks** : Open networks in WPA2 send user traffic unauthenticated 

*Remediation : WPA3 dot not use authentication ==> use Opportunistic Wireless Encryption (OWE) to encrypt all wireless traffic*


- **Internet of Things (IoT) Onboarding :** WPA2 included Wi-Fi Protected Setup (WPS) ==> vulnerable to attacks ==> IoT devices ==> no build-in GUI ==> provide easy way to get connected to wireless network ==> Device Provisioning Protocol (DPP) designed to address this need

# ==XIII. WLAN Configuration==

## Wireless Router

![[Pasted image 20231012132321.png]]

**Include :**

- Switch for wired clients
- port for internet connection "WAN"
- Wireless components

![[Pasted image 20231012132523.png]]

- WLAN security
- DHCP services
- Name Address Translation (NAT)
- Quality of Service (QoS)
- Others features..

## Log in to Wireless Router

*Preconfigured to be connected to the network and provide services*

- On internet we can see username and passwords

## Basic Network Setup

1. Log in the router from a web browser
2. Change the default administrative password
3. Log in with the new administrative password
4. Change the default DHCPv4 IPv4 addresses
5. Renew the IP address
6. Log in to the router with the new IP address

## Basic Wireless Setup

- View the WLAN defaults

![[Pasted image 20231012173629.png]]

- Change the network mode

![[Pasted image 20231012173648.png]]

- Configure SSID

![[Pasted image 20231012173731.png]]

- Configure channel

![[Pasted image 20231012173748.png]]

- Configure security mode

![[Pasted image 20231012173802.png]]

- Configure passphrase

![[Pasted image 20231012173817.png]]

## Configure Wireless Mesh Network (extend range)

- Select channels precisely to not interfere
- 1, 6, 11 channels 

![[Pasted image 20231012174147.png]]

## NAT for IPv4

- **Private source IPv4 address translated to a public address

![[Pasted image 20231012174314.png]]

- Need of NAT to be routed on the internet
- Keep track of open ports behind NAT

## Quality of Service

- Prioritized Traffic
- Voice and video prioritized over email and Web Browsing

![[Pasted image 20231012174834.png]]

## Port Forwarding

- Open specific ports

![[Pasted image 20231012175048.png]]

- Port triggering ==> temporarily forward data through inbound ports to a specific device 

## WLC Topology


![[Pasted image 20231013160011.png]]

- **Lightweight APS (LAPS)** use **Lightweight Access Point Protocol (LWAPP)** to communicate with a **WLAN controller (WLC)**

![[Pasted image 20231013160024.png]]

- AP is PoE (Powered over the Ethernet)

## Log in to the WLC

- provides services, managements capabilities

![[Pasted image 20231013160356.png]]

*Cisco 3504 Wireless Controller here*

- **Network Summary** ==> view of number of configured wireless networks, APs, active clients, rogues APs and clients

![[Pasted image 20231013160536.png]]

- **Access points** ==> view of AP name, IP, Cisco Discovery Protocol (CDP) active, channels, number of clients, MAC address, etc

![[Pasted image 20231013160731.png]]

**AP** here is **Cisco Aironet 1815i** 

**Ping R1, WLC and see interface wired 0 !**

![[Pasted image 20231013161038.png]]
## Advanced Settings on WLC

![[Pasted image 20231013163336.png]]
## Steps to Configure WLAN

- Cisco 3504 WLC support 150 access points and 4096 VLANs
- 5 physical ports (essentially for trunk ports carry traffic from multiple VLANs to a switch)

![[Pasted image 20231013171320.png]]

- Each AP can support multiple WLANs
### **Steps to make a WLAN**

- Create the WLAN

![[Pasted image 20231013230845.png]]

- Apply and Enable the WLAN

![[Pasted image 20231013230912.png]]

- Select the interface

![[Pasted image 20231013230933.png]]

 - Secure the WLAN

![[Pasted image 20231013230955.png]]

- Verify the WLAN is Operational

![[Pasted image 20231013231013.png]]

- Monitor the WLAN

![[Pasted image 20231013231031.png]]

- View Wireless Client Details

![[Pasted image 20231013231048.png]]

## SNMP and RADIUS

- Simple Network Management Protocol (SNMP) and Remote Authentication Dial-In User Services (RADIUS)
- SNMP monitor the network

![[Pasted image 20231013232719.png]]

- WLC forward SNMP log messages (called trap) to the SNMP server
- RADIUS server for authentication, authorization and accounting (AAA) services (Required for WLANs using WPA2 enterprise authentication)
## Configure SNMP Server Information

![[Pasted image 20231013233110.png]]

- Enter SNMP community name, IP address of SNMP server

![[Pasted image 20231013233224.png]]
## Configure RADIUS Server Information

![[Pasted image 20231013233323.png]]

- Enter IPv4 address for PC-A and shared secret (password used between WLC and RADIUS server)

![[Pasted image 20231013234131.png]]

## Topology with VLAN 5 Addressing

- Each WLAN config on WLC need a virtual interface

![[Pasted image 20231014102757.png]]

- 5 physical ports for data traffic ==> each physical port support multiple WLANs each on its own virtual interface

- We can aggregated physical ports to create high-bandwidth links

![[Pasted image 20231014103052.png]]

New WLAN use interface VLAN 5 and network 192.168.5.0/24

## Configure a New Interface

- Create a new interface

![[Pasted image 20231014104144.png]]

- Configure VLAN name and ID

![[Pasted image 20231014104202.png]]

- Configure the port and interface address (Port number, IP address, Network, Gateway)

![[Pasted image 20231014104244.png]]

- Configure DHCP server IP address

![[Pasted image 20231014104259.png]]

- Verify Interface

![[Pasted image 20231014104324.png]]

## Configure a DHCP Scope

- Create a new DHCP Scope

![[Pasted image 20231014104835.png]]

- Name the DHCP Scope

![[Pasted image 20231014104909.png]]

- Verify the New DHCP Scope

![[Pasted image 20231014104926.png]]

- Configure and enable the new DHCP Scope (Start Address, End Address, Network, Netmask, Default Routers, status)

![[Pasted image 20231014104944.png]]

## Configure a WPA2 Enterprise WLAN

- Create new WLAN

![[Pasted image 20231014111423.png]]

- Configure WLAN name and SSID

![[Pasted image 20231014113408.png]]

- Enable WLAN for VLAN 5

![[Pasted image 20231014113450.png]]

- Verify AES and 802.1X defaults

![[Pasted image 20231014113514.png]]

- Configure WLAN security to use the RADIUS server

![[Pasted image 20231014113555.png]]

- Verify new WLAN

![[Pasted image 20231014113632.png]]

## Troubleshooting Approaches

![[Pasted image 20231014151003.png]]
### Wireless Client Not Connecting

![[Pasted image 20231014151200.png]]

**If no connectivity**

- Check network configuration : **ipconfig** (does the client have a IP address ?)
- Device connected to the wired network ? ping a know IP address
- Reload driver, try a different wireless NIC
- If Wireless NIC working check security mode and encryption settings

**Connectivity performing poorly**

- How far PC from an AP ? (out of the planned BSA ?)
- Check channel settings on wireless client
- Presence of other device with 2.4 GHz band (rogue APs, microwave ovens, baby monitors, cordless phones)

**Physical checks**

- Devices are power on ?
- Bad connectors, damaged or missing cables ?

**When all checks done** ==> investigating configuration, performance and power status of the AP

## Troubleshooting When the Network Is Slow

- **Upgrade wireless devices** : Support highest standards


- **Split the traffic** : 
	- Split between the 802.11n 2.4 GHz band and the 5 GHz band
	- Use 802.11n can have two bands for 2 separate wireless networks
		- 2.4 GHz network (suitable for not time-sensitive) for basic internet tasks (web browsing, email, download)
		- 5 GHz network (less crowded => ideal ) for streaming multimedia

![[Pasted image 20231014152057.png]]

- 5 GHz band has more channels => likely interference-free
- Ensure AP are free of obstructions => if does not solve the problem ==> Wi-Fi Range Extender

## Updating Firmware

- Fixes common problems, security vulnerabilities
- WLC can upgrade firmware of AP 

![[Pasted image 20231014153630.png]]

# ==XIV. Routing Concepts==

## Functions of Routers

- **Router determine best path for the packet** 

- **Connects multiple networks**, **multiple interfaces** that **each belong to a different IP network**

- **When the router receives an IP packet on one interface**, it will **determine which interface to use to forward the packet to the destination**

- **Each network that a router connects to requires a separate interface but this may not always be the case

## 2 Mains Functions of Routers

- **Determine best path to forward packets based on the information in its routing table

- **Forward packets toward their destination**

![[Pasted image 20231011190411.png]]

![[Pasted image 20231011190434.png]]
## Best Path Equals Longest Match

**Routing table contains route entries** consisting of a **prefix (network address)** and **prefix length**

Remember that an **IP packet only contains the destination IP address** and **not the prefix length**

The route with the **greatest number of equivalent far-left bits or longest match** is always the **preferred route**

## IPv4 Address Longest Match Example

- The **longest match would be chosen** to forward the packet

![[Pasted image 20231011192033.png]]

## IPv6 Address Longest Match Example

- For the destination IPv6 packet : 2001:dc8:c000::99

![[Pasted image 20231011192331.png]]

## Build the Routing Table

*Routing table consist of prefixes and their prefix lengths*

![[Pasted image 20231011204452.png]]

**How does R1 in the figure populate its routing table**

## 1. Directly Connected Networks

- **Directly connected network** is **added to the routing table** when an interface is configured with an **IP address and subnet mast (prefix length)** and is active 

## 2. Remote Networks

- **Networks not directly connected to the router** 
	- **Static routes** : Added to the routing table when a route is manually configured
	- **Dynamic routing protocols** : Added to the routing table when routing protocols dynamically learn about the remote network. (Enhanced Interior Gateway Routing Protocol (EIGRP), Open Shortest Path First (OSPF) and others

## 3. Default Route

- **Default route specifies a next-hop router** to use when we **don't have a specific route that matches the destination IP address**

- **Default route** can be **entered manually as a static route** or **learned automatically from a dynamic routing protocol**

- For IPv4 route entry : 0.0.0.0/0 
- For IPv6 route entry ::/0

The **/0 prefix length indicates zero bits need to match** the destination IP address.

## Packet Forwarding Decision Process

![[Pasted image 20231014175829.png]]

### Forwards the Packet to a Device on a Directly Connected Network

- Packet **forwarded to the destination device
- Must be **encapsulated in Ethernet frame
	- **IPv4 packet** : checks ARP table for destination IPv4 address and associated Ethernet MAC address ==> elif sends ARP request and get MAC address
	- **IPv6 packet** : checks neighbor cache and an associated Ethernet MAC address ==> elif send ICMPv6 Neighbor Solicitation (NS) and get MAC address
### Forwards the Packet to a Next-Hop Router

- Forward to next-hop router
- Check for the IP address of the next-hop router in its ARP table or neighbor cache

### Drops the Packets - No Match in Routing Table

- Packet dropped

## End-to-End Packet Forwarding

- Change source MAC Address and Destination MAC address every hop
- No Mac Address on P2P link

## Packet Forwarding Mechanisms

*3 packet forwarding mechanism

- **Process switching** (old mechanism)
	- Packet arrives on interface -> forward to control plane -> check matches for destination address in the routing table -> determine exit interface -> forward packet
	- Does this for every packets even if the destination is the same !! (very slow, rarely implemented in modern networks)

![[Pasted image 20231014185843.png]]

- **Fast switching** (old mechanism, successor of process switching)
	- use of fast-switching cache to store next-hop information
	- Packet arrives on interfaces -> forward to control plane -> match in the fast-switching cache -> if not there -> process-switched -> forward to exit interface

![[Pasted image 20231014185904.png]]

- **Cisco Express Forwarding (CEF, most recent and default Cisco IOS packet-forwarding mechanism)
	- Build *F*orwarding *I*nformation *B*ase (FIB) and adjacency table 
	- Table entry are Change-triggered (something changes in the topology)

![[Pasted image 20231014185917.png]]

- Use on Routers and L3 switches
- 5 packets are quickly processed in the data plane

### Router Basic Configuration

```
enable
configure terminal
hostname R2
enable secret c1sco1234
ip domain-name ccna-lab.com
no ip domain lookup
service password-encryption
username SSHadmin secret 55Hadm!n
crypto key generate rsa
1024

line console 0
password cisco
logging synchronous
exec-timeout 6 0
login

line vty 0 4
password cisco
exec-timeout 6 0
transport input ssh
login local

banner motd $ WARNING Authorized Users Only! $
ipv6 unicast-routing

interface g0/0/0
description Connection to S3
ip address 10.0.4.1 255.255.255.0
ipv6 address fe80::2:a link-local
ipv6 address 2001:db8:acad:4::1/64
no shutdown

interface g0/0/1
description Connection to S4
ip address 10.0.5.1 255.255.255.0
ipv6 address fe80::2:b link-local
ipv6 address 2001:db8:acad:5::1/64
no shutdown

interface s0/1/0
description Link to R1
ip address 10.0.3.2 255.255.255.0
ipv6 address fe80::1:c link-local
ipv6 address 2001:db8:acad:3::2/64
no shutdown

interface s0/1/1
description Link to Internet
ip address 209.165.200.225 255.255.255.252
ipv6 address fe80::1:d link-local
ipv6 address 2001:db8:feed:224::1/64
no shutdown
end

copy running-config startup-config
```

## Verification Commands

- **show ip interface brief**
- **show ipv6 interface brief**
- **show running-config interface g0/0/0
- **show interfaces
- **show ip interface
- **show ipv6 interface
- **show ip route
- **show ipv6 route
- **ping**

## Filter Command Output

- **section** : entire section match
- **include** : all output lines match
- **exclude** : all-non output lines match
- **begin** : output lines from a certain point

*Example :

![[Pasted image 20231017195252.png]]

### Route Sources 

- R1, R2 used OSPF to share routing information
- R2 have default route to ISP

![[Pasted image 20231023123544.png]]

**R1**

![[Pasted image 20231023123655.png]]

**R2**

![[Pasted image 20231023123715.png]]

## Routing Table Principles

- **Decision alone based on own routing table
- **Information in a routing table of one router** does **not necessarily match the routing table of another router
- Routing information about a path does **not provide return routing information

## Routing Table Entries

![[Pasted image 20231023124724.png]]

- Learn from OSPF

![[Pasted image 20231023124908.png]]

- C : Connected <=> **active interface configured with IP address and subnet mask (prefix length)

- L : Local <=> **directly connected address

## Static Routes

- Define explicit path between two networking devices 
- Improve security
- Ressource efficiency
- Used less bandwidth than dynamic routing protocols
- No need of calculate and communicate routes

**Disadvantage**

- lack of automatic reconfiguration if network topology changes

**Primary uses**

- Smaller networks
- Default route used to send traffic to any destination beyond the next upstream router
- Routes to and from stub network (stub network accessed by a single route and router have only one neighbor)

![[Pasted image 20231023133824.png]]

- **R1 put a default static route to R2 as the next hop for all other networks

## Static Routes in the IP Routing Table

![[Pasted image 20231023134304.png]]

![[Pasted image 20231023134342.png]]

## Dynamic Routing Protocols

- Share information about reachability and status of remote networks
- Network discovery and maintaining routing tables

**Advantages**

- Select best path
- Discover a new best path when change in the topology

![[Pasted image 20231023134647.png]]

![[Pasted image 20231026123129.png]]
## Default Route

- Similar to a default gateway on a host
- Next-hop router when the routing table does not contain a specific route that matches the destination IP address
- Static route or learned from a dynamic routing protocol 
- 0.0.0.0/0 for IPv4 or ::/0 for IPv6


![[Pasted image 20231023135427.png]]

![[Pasted image 20231026123311.png]]

## Structure of an IPv4 Routing Table

- **Classful Addressing**

![[Pasted image 20231026123417.png]]
![[Pasted image 20231026123703.png]]
## Structure of an IPv6 Routing Table

![[Pasted image 20231026123830.png]]

## Administrative Distance

**Routing table learns** about the same network address from **more than one routing source

- **Different dynamic routing protocol can be implemented on a router

**AD** for **Administrative Distance** to determine which route to install in the IP routing table

![[Pasted image 20231026124344.png]]

## Static or Dynamic ?

**Static Routes**

- As a default route forwarding packets to a service provider
- For routes outside the routing domain and not learned by the dynamic routing protocol
- When the network administrator wants to explicitly define the path for a specific network
- For routing between stub networks

*In smaller networks with only one path to an outside network => provide security in larger network for certain types of traffic or links to other networks that need more control*

**Dynamic Routing Protocols**

- In networks consisting of more than just a few routers
- When a change in the network topology requires the network to automatically determine another path
- For scalability, as the network grows the dynamic routing protocol automatically learns about any new networks

![[Pasted image 20231026125244.png]]
## Dynamic Routing Evolution 

- Used since 1980

![[Pasted image 20231026130931.png]]

**First routing Protocol** => **RIPv1 based on ARPANET** Advanced Research Projects Agency Network **early 1969

**RIPv2 does not scale larger network** => **OSPF**, **IS-IS Intermediate System-to-Intermediate System**, **Cisco developed Interior Gateway Routing Protocol (IGRP)

**Need to connect different routing domains of different organizations and provide routing => Border Gateway Protocol (BGP)

![[Pasted image 20231026131059.png]]

To support IPv6 communication, newer versions of IP routing protocols have been developed 

- **IGP I**nterior **G**ateway **P**rotocols => routing protocol to **exchange routing information within a routing domain administered by a single organization** 

- **EGP** **E**xterior **G**ateway **P**rotocols => **exchange routing information between different organizations known as Autonomous Systems (AS)** 

- **Path vector, distance vector or link-state** refer type of routing algorithm used to determine best path

![[Pasted image 20231026131841.png]]
 
## Dynamic Routing Protocol Concepts

*Purpose of dynamic routing protocol

- **Discovery of remote networks**
- **Maintaining up-to-date routing information**
- **Choosing the best path to destination networks**
- **Ability to find a new best path if the current path is no longer available**

**Main components of dynamic routing protocols**

- **Data Structures** : use tables or databases, information kept in RAM

- **Routing protocol Messages** : various type of messages to discover neighboring routers, exchange routing information, learn and maintain accurate information about the network

- **Algorithm** : List of steps to accomplish a task => for facilitating routing information and best path determination
## Best Path

Dynamic routing protocols use their own rules and metrics to build and update routing tables

![[Pasted image 20231026132624.png]]

## Load Balancing

- When a **router has two or more paths to a destination with equal cost metrics** => router **forwards the packets using both paths equally

![[Pasted image 20231026132912.png]]

*Equal coxst Load Balancing*
# ==XV. IP Static Routing==

## Types of Static Routes

- Standard static route
- Default static route
- Floating static route
- Summary static route

## Next-Hop Options

- **Next-hop route** : Only the next-hop IP address is specified
- **Directly connected static route** : Only the router exit interface is specified
- **Fully specified static route** : The next-hop IP address and exit interface are specified

### IPv4 Static Route Command

```
Router(config)# ip route network-address subnet-mask { ip-address | exit-intf [ip-address]} [distance]
```

![[Pasted image 20231107112724.png]]

### IPv6 Static Route Command

```
Router(config)# ipv6 route ipv6-prefix/prefix-length {ipv6-address | exit-intf [ipv6-address]} [distance]
```

![[Pasted image 20231107112950.png]]

### Dual-Stack Topology

- No static routes configured for either IPv4 or IPv6

![[Pasted image 20231107113104.png]]

## IPv4 Starting Routing Table

- **R1 IPv4 Routing Table

![[Pasted image 20231107113241.png]]

- **R2 IPv4 Routing Table

![[Pasted image 20231107113337.png]]

- **R3 IPv4 Routing Table

![[Pasted image 20231107113926.png]]

- **R1 ping R2**

![[Pasted image 20231107114201.png]]

- **R1 connot ping R3 LAN**

![[Pasted image 20231107114232.png]]

## IPv6 Starting Routing Tables

- **R1 IPv6 Routing Table

![[Pasted image 20231107114511.png]]

- **R2 IPv6 Routing Table**

![[Pasted image 20231107114703.png]]

- **R3 IPv6 Routing Table**

![[Pasted image 20231107114720.png]]

- **R1 can ping R2

![[Pasted image 20231107114917.png]]

- **R1 Cannot Ping R3 LAN**

![[Pasted image 20231107114926.png]]

![[Pasted image 20231107115533.png]]

**IPv4 Route Commands**

```
R1(config)# ip route 172.16.1.0 255.255.255.0 172.16.2.2
R1(config)# ip route 192.168.1.0 255.255.255.0 172.16.2.2
R1(config)# ip route 192.168.2.0 255.255.255.0 172.16.2.2
```

**R1 Routing Table**

![[Pasted image 20231107115837.png]]

### IPv6 Next-Hop Static Route


```
R1(config)# ipv6 unicast-routing
R1(config)# ipv6 route 2001:db8:acad:1::/64 2001:db8:acad:2::2
R1(config)# ipv6 route 2001:db8:cafe:1::/64 2001:db8:acad:2::2
R1(config)# ipv6 route 2001:db8:cafe:2::/64 2001:db8:acad:2::2
```

**R1 Routing Table**

![[Pasted image 20231110115908.png]]

### IPv4 Directly Connected Static Route

![[Pasted image 20231110120531.png]]

```
R1(config)# ip route 172.16.1.0 255.255.255.0 s0/1/0
R1(config)# ip route 192.168.1.0 255.255.255.0 s0/1/0
R1(config)# ip route 192.168.2.0 255.255.255.0 s0/1/0
```

![[Pasted image 20231110120829.png]]

### IPv6 Directly Connected Static Route

```
R1(config)# ipv6 route 2001:db8:acad:1::/64 s0/1/0
R1(config)# ipv6 route 2001:db8:cafe:1::/64 s0/1/0
R1(config)# ipv6 route 2001:db8:cafe:2::/64 s0/1/0
```

![[Pasted image 20231110121119.png]]

![[Pasted image 20231110130953.png]]
### IPv4 Fully Specified Static Route

- both exit interface and next-hop IP address are specified

```
R1(config)# ip route 172.16.1.0 255.255.255.0 GigabitEthernet 0/0/1 172.16.2.2
R1(config)# ip route 192.168.1.0 255.255.255.0 GigabitEthernet 0/0/1 172.16.2.2
R1(config)# ip route 192.168.2.0 255.255.255.0 GIgabitEthernet 0/0/1 172.16.2.2
```

![[Pasted image 20231110131433.png]]

### IPv6 Fully Specified Static Route

![[Pasted image 20231110131531.png]]

```
R1(config)# ipv6 route 2001:db8:acad:1::/64 fe80::2
%Interface has to be specified for a link-local nexthop
R1(config)# ipv6 route 2001:db8:acad:1::/64 s0/1/0 fe80::2
```

Reason for fully specified route => IPv6 link-local addresses are not contained in the IPv6 routing table

```
R1# show ipv6 route static | begin 2001:db8:acad:1::/64
S 2001:DB8:ACAD:1::/64 [1/0] 
  via FE80::2, Seria0/1/0
```

### Verify Static Route

- **show ip/ipv6 route static**
- **show ip/ipv6 route** network
- **show running-config | section ip/ipv6 route

![[Pasted image 20231110132147.png]]

![[Pasted image 20231110132236.png]]

![[Pasted image 20231110132253.png]]

![[Pasted image 20231110132306.png]]

![[Pasted image 20231110132406.png]]

![[Pasted image 20231110132436.png]]

### Default Static Route

- single default route for internet

![[Pasted image 20231110133212.png]]

- Default route configured locally or learned form another router using dynamic routing protocol
- Default static routes used when connecting an edge router to a service provider network or a stub router

**IPv4 Default Static Route

```
Router(config)# ip route 0.0.0.0 0.0.0.0 {ip-address | exit-intf}
```

**IPv6 Default Static Route

```
Router(config)# ipv6 route ::/0 {ipv6-address | exit-intf}
```

### Configure a Default Static Route

![[Pasted image 20231110134311.png]]

```
R1(config)# ip route 0.0.0.0 0.0.0.0 172.16.2.2
```

```
R1(config)# ipv6 route ::/0 2001:db8:acad:2::2
```

### Verify a Default Static Route

![[Pasted image 20231110134440.png]]

**IPv4 Default Static Route**

- asterisk represent a static route <=> Gateway of last resort is 172.16.2.2 to network 0.0.0.0

![[Pasted image 20231110134516.png]]

**IPv6 Default Static Route**

![[Pasted image 20231110134624.png]]

- none of the bits are require to match
- as more specific match does not exist => default static route matches all packets 

### Floating Static Routes

- Routes used to provide a **backup path to a primary static or dynamic route** (when link failure)
- **Higher administrative distance** than primary route

In the case of **EIGRP**-learned route

- **administrative distance of 90**
- **put 95 administrative distance to floating static route**
- if EIGRP-learned route **lost** => **floating static route used

![[Pasted image 20231110135432.png]]

- Static routes by default have administrative distance of 1

**Administrative distances**

- EIGRP = 90
- OSPF = 110
- IS-IS = 115

### Configure IPv4 and IPv6 Floating Static Routes

- **if no administrative distance is configured** => **default value 1 is used

![[Pasted image 20231110135733.png]]

```
R1(config)# ip route 0.0.0.0 0.0.0.0 172.16.2.2
R1(config)# ip route 0.0.0.0 0.0.0.0 10.10.10.2 5
R1(config)# ipv6 route ::/0 2001:db8:acad:2::2
R1(config)# ipv6 route ::/0 2001:db8:feed:10::2 5
```

- **IPv4** and **IPv6 floating static default routes** pointing to **R3** with **administrative distance of 5

![[Pasted image 20231110140207.png]]

- **IPv4 floating static route to R3 is not present in the routing table**

![[Pasted image 20231110140318.png]]

- **IPv4 floating static** route **appear when link fail**

![[Pasted image 20231110140441.png]]

### Configure Static Host Routes

3 ways host route can be added to the routing table

- **Automatically installed when an IP address is configured on the router**
- **Configure as a static host route**
- Host route automatically obtained **through other methods (later)**

### Automatically Installed Host Routes

- Interface address configure on the router

![[Pasted image 20231110141614.png]]

- IPv4 Mask /32 and IPv6 Mask /128

![[Pasted image 20231110141957.png]]
### Static Host Routes

- Static route to a specific destination device ==> such as server
### Configure Static Host Routes

```
Branch(config)# ip route 209.165.200.238 255.255.255.255 198.51.100.2
Branch(config)# ipv6 route 2001:db8:acad:2::238/128 2001:db8:acad:1::2
Branch(config)# exit
```

## Verify Static Host Routes

![[Pasted image 20231110142739.png]]

### Configure IPv6 Static Host Route with Link-Local Next-Hop

- Specify interface type and interface number

![[Pasted image 20231110142905.png]]

# ==XVI. Troubleshoot Static and Default Routes==

### Common Troubleshooting Commands

- **ping**
- **traceroute**
- **show ip route**
- **show ip interface brief**
- **show cdp neighbors detail**

![[Pasted image 20231110190316.png]]

![[Pasted image 20231110190335.png]]

![[Pasted image 20231110190344.png]]

![[Pasted image 20231110190356.png]]

![[Pasted image 20231110190426.png]]

- Show Directly Connected Cisco devices (cdp)

![[Pasted image 20231110190514.png]]

### Solve a Connectivity Problem

- PC1 can't access ressources on R3 LAN

![[Pasted image 20231110190700.png]]

- **Ping the remote LAN**

ping from R1 on one interface g0/0/0 which belong to the network of the user

![[Pasted image 20231110190710.png]]

- **Ping the Next-Hop Router**

![[Pasted image 20231110190837.png]]

- **Ping R3 LAN from S0/1/0**

R3 have a route back to network

![[Pasted image 20231110190912.png]]

- **Verify R2 Routing Table**

Missconfig => 172.16.3.0 have next hop on 192.168.1.1 it should be 172.16.2.1 

![[Pasted image 20231112160819.png]]

- **Correct R2 Static Route Configuration**

![[Pasted image 20231112161047.png]]

- **Ping success**

![[Pasted image 20231112161132.png]]
