
# ==I. Single-Area OSPFv2 Concepts==

## OSPF (Open Shortest Path First)

- **Single-rea**
- **Multiarea**

- **OSPFv2 for IPv4 Networks**
- **OSPFv3 for IPv6 Networks**

## Link-State Routing Protocol

*alternative for the distance vector Routing Information Protocol (RIP)*

- **RIP** acceptables in the early day (rely on **hop counts for best route**)
- do **not scale well** in larger networks **with multiple paths of varying speeds

**OSPF significant advantages over RIP** => **faster convergence and scales** to larger network

- **Concept of area 
- **State of a link known as a link-state (network prefix, prefix length, cost)**
## Components of OSPF

#### Routing Protocol Messages : 

Routers running OSPF exchange messages to convey routing information using **five types of packets**

- **Hello packet**
- **Database description packet**
- **Link-state request packet**
- **Link-state update packet**
- **Link-state acknowledgment packet**

![[Pasted image 20231113161356.png]]

OSPF Messages to **discover neighboring routers** and **exchange routing information to maintain accurate information about the network**
#### Data Structures :

**OSPF Messages** to **create** and **maintain three OSPF databases** :

- **Adjacency database :** creates neighbor table
- **Link-state database (LSDB) :** create the topology table
- **Forwarding database :** creates the routing table

![[Pasted image 20231113161745.png]]

#### Algorithm :

- **Build topology table using Dijkstra shortest-path first (SPF) algorithm
- **Based on cumulative cost to reach a destination**

*SPF algorithm creates an SPF tree => placing each router at the root of the three ==> calculating shortest path to each node ==> place the best routes in forwarding database ==> used to make the routing table*

![[Pasted image 20231113162218.png]]

## Link-State Operation

#### 1. Establish Neighbor Adjacencies

- **OSPF-enabled routers => recognize each other on the network
- Send **Hello packets to OSPF-enabled interfaces** to **determine if neighbors are present on those links
- If **neighbor present** => OSPF-enable router attempts to establish a **neighbor adjacency with that neighbor**

![[Pasted image 20231113162856.png]]

#### 2. Exchange Link-State Advertisements

- Exchange of **link-state advertisements (LSAs)**
- LSA contain **state and cost of each directly connected link**

![[Pasted image 20231113175540.png]]

- Router **flood their LSAs to adjacent neighbors until all routers in the area have all LSAs

#### 3. Build the Link State Database

- **After LSAs received** ==> **build topology table (LSDB)** ==> contain information about the topology of the area

![[Pasted image 20231113175930.png]]

#### 4. Execute the SPF Algorithm

- **Execute SPF algorithm** => **create SPF tree**

![[Pasted image 20231113180108.png]]

#### 5. Choose the Best Route

- **After SPF tree is build** ==> **best paths to each network** 
- **Route will be inserted into routing table unless** there is a route source to the same network with lower administrative distance ==> such as static route

![[Pasted image 20231113180332.png]]

## Single-Area and Multiarea OSPF

**To make OSPF more efficient and scalable**

- Support hierarchical routing using **areas**
- **OSPF area** => group of routers that share the **same link-state information in their LSDBs**

#### Single-Area

- **All routers are in one area => best practice to use area 0

![[Pasted image 20231113181116.png]]

#### Multiarea OSPF 

- **Multiple area**, all areas must connect to the backbones area 0 
- Routers interconnecting area are **Area Border Routers (ABRs)**

![[Pasted image 20231113181411.png]]

- **One large routing domain can be divided into smaller areas** to **support hierarchical routing
- Routing occurs between areas (**interarea routing**) 
- When router **receives a new information about a topology change** within the area (**including the addition, deletion or modification of a link**) => **rerun SPF algorithm** => create **new SPF tree, update routing table**

SPF algorithm **CPU-intensive** and the **time it takes for calculation depends on the size of the area** 


**Hierarchical-Topology Design**

- **Smaller routing tables** : fewer routing table entries => network addresses summarized between areas => route summarization not enable by default

- **Reduced link-state update overhead** : Designing multiarea OSPF with smaller areas minimizes processing and memory 

- **Reduced frequency of SPF calculations** : Multiarea OSPF localize impact of a topology change within an area => minimizes routing update impact => LSA flooding stops at the area boundary

![[Pasted image 20231114101455.png]]

### OSPFv3

- Equivalent of **OSPFv3 with exchanging IPv6 prefixes

**In IPv6**

=> network address => prefix
=> subnet mask => prefix-length

**OSPFv3 separate processes form its IPv4 counterpart**

- OSPFv2 and OSPFv3 have separate adjacency tables
- OSPF topology tables
- IP routing tables

![[Pasted image 20231114102041.png]]

## Type of OSPF Packets

**Link-state packets (LSPs)** => establish and maintain neighbor adjacencies and exchange routing updates

- **Type 1 : Hello packet** : establish and maintain adjacency with other OSPF routers

- **Type 2: Database Description (DBD) packet :** contains an abbreviated list of the LSDB of the sending router => used by receiving routers to check against local LSDB => must be identical within an area to construct an accurate SPF tree

- **Type 3 : Link-State Request (LSR) packet :** Receiving routers can then request more information about any entry in the DBD by sending an LSR

- **Type 4 : Link-State Update (LSU) packet :** Used to reply to LSRs and announce new information => LSU contain several different types of LSAs

- **Type 5 : Link-State Acknowledgement (LSAck) packet :** When LSU received => sends an LSAck to confirm receipt of the LSU => LSAck data field is empty

![[Pasted image 20231114102956.png]]

## Link-State Updates

- **Type 3 LSR** => **request more information about an entry in the DBD**
- **Type 4 LSU** => **reply to an LSR packet
- **Type 5 LSAck** => **acknowledge the receipt of a Type 4 LSU

**LSU contain 11 different types of OSPFv2 LSAs** 

**OSPFv3 renamed several of thee LSAs** => **two additional LSAs**

*Difference between LSU and LSA can sometimes be confusing => LSU contain one or more LSAs*

![[Pasted image 20231114104309.png]]

### Hello Packet

**OSPF Type 1 packet**

- **Discover OSPF neighbors** and **establish neighbor adjacencies
- **Advertise parameters on which two routers must agree to become neighbors
- **Elect the Designated Router (DR)** and **Backup Designated Router (BDR)** on multiaccess networks like Ethernet => Point-to-point links do not require DR or BDR

![[Pasted image 20231114104733.png]]

### OSPF Operational States

- **Create adjacencies with neighbors**
- **Exchange routing information**
- **Calculate the best routes**
- **Reach convergence**

![[Pasted image 20231114111011.png]]

### Establish Neighbor Adjacencies

- **If OSPF enabled on an interface** ==> determine if there is **another OSPF neighbor on the link** => **HELLO packet with router ID out all OSPF-enabled interfaces**

- **Send to OSPF Routers IPv4 multicast address : 224.0.0.5** => **OSPFv2 process these packets

- **Router ID** is a **32 bit number** like an IPv4 address 

- **if neighboring OSPF-enabled router receives a Hello packet with router ID** that is **not within its neighbor list ==> attempts to establish an adjacency with the initiating router

#### 1. Down State to Init State

When **OSPFv2 is enabled** => **enabled Gigabit Ethernet 0/0 interface** transitions **from Down state to Init state

- **R1 start sending Hello packets out all OSPF-enabled interfaces** to **discover OSPF neighbors to develop adjacencies**

![[Pasted image 20231114112046.png]]

#### 2. The Init State 

- **R2 receive Hello Packet from R1 => add router ID to its neighbor list**
- **R2 sends Hello packet to R1**

![[Pasted image 20231114112248.png]]

#### 3. Two-Way State

- **R1 receives Hello => add R2 Router ID to its OSPF neighbors (It notices its own Router ID is in the list of neighbors of the Hello Packet)

- **Transition to Two-Way State :**

	- **if interconnected** with **point-to-point link** => **Transition from Two-Way state** to the **ExStart state
	- If **interconnected** over **Ethernet network** => designated router DR and BDR must be elected 

![[Pasted image 20231114112318.png]]

#### 4. Elect the DR and BDR

- Based **on priority** and **Router ID**

![[Pasted image 20231114112746.png]]

### Synchronizing OSPF Databases

**After Two-Way state** => transition to **database synchronization states**

- 1. **Decide first router**

In the **ExStart state** => **router with higher router ID** will **send DBD packet first** during the **Exchange state**

![[Pasted image 20231114113745.png]]

- 2. **Exchange DBDs**

In the **Exchange state** => **two routers** exchange **one or more DBD packets**

**DBD packet** => **information** about the **LSA entry header** that appears in the **LSDB of the router**

Each **LA entry header** => **information about the link-state type**, **address of the advertising router**, **cost of the link and the sequence number** 

**Sequence number** => determiner newness of the received link-state information

![[Pasted image 20231114114244.png]]

- 3. **Send an LSR**

**R1 compare information** received with its **own LSDB**

**If DBD packet has more current link-state entry** => transition **to Loading state

**LSUs** send to neighbors :

- When a change 
- Every 30 minutes

![[Pasted image 20231114174700.png]]


## The Need for a DR

Multiaccess network create two challenges :

- **Creation of multiple adjacencies** : Interconnect many OSPF routers => **creating adjacencies with every router** => **excessive number of LSAs** on the same network

- **Extensive flooding of LSAs** : Link-state routers **flood their LSAs when change => become excessive**

**Formule :** For any number of router (n)on a network => there are n (n - 1) / 2 adjacencies

In the case of 5 routers = 5 (5 - 1) / 2 = 10 adjacencies

Without this technique **20 routers would create 190 adjacencies

![[Pasted image 20231114175421.png]]

## LSA Flooding With a DR

**Flooding LSA :**

- **Flood** and **acknowledge all received LSAs**

![[Pasted image 20231114175707.png]]

**LSAs and DR**

- **OSPF elect DR** to be the **collection** and **distribution point for LSAs send and received**
- **BDR elected in case DR fails**

![[Pasted image 20231114180126.png]]

- **All others routers become DROTHERS** => neither DR nor BDR
# ==II. Single-Area OSPFv2 Configuration==

### Router Configuration Mode for OSPF

**OSPF enabled** using **router ospf process-id**

- Number between 1 to 65 535 selected by network administrator

![[Pasted image 20231115094958.png]]

- **Router ID** uniquely **identify an OSPF router => to participate in an OSPF domain

- **Defined by administrator or automatically assigned by the router**

**Router ID used to :

- **Participate in the synchronization of OSPF databases** : In Exchange State => router with highest router ID send their database descriptor (DBD) packet first

- **Participate in the election of the designated router (DR) :** Router with **highest router ID elected as DR** => the **second highest router ID** is elected **backup designated router (BDR)**

### Router ID Order of Precedence


- 1. **Router ID explicitly configured**
- 2. **Choose highest IPv4 address of loopback
- 3. **Highest active IPv4 address of its physical interfaces**

![[Pasted image 20231115155218.png]]

### Configure a Loopback Interface as the Router ID

If loopback interfaces **have not been configured and active**,  **no explicitly configured Router ID

```
R1(config-if)# interface Loopback 1
R1(config-if)# ip address 1.1.1.1 255.255.255.255
R1(config-if)# end
R1# show ip protocols | include router ID
	Router ID 1.1.1.1
```

### Explicitly Configure a Router ID

![[Pasted image 20231115155525.png]]

```
R1(config)# router ospf 10
R1(config-router) router-id 1.1.1.1
R1(config-router) end
R1# show ip protocols | include Router ID
Router ID 1.1.1.1
```

### Modify a Router ID

**After a router select a Router ID** => **not allow to change router ID until router is reloaded or OSPF process is reset

![[Pasted image 20231115155958.png]]

here we can see the router **ID 10.10.1.1 from a loopback interface** => after **clear ip ospf process** and **modification of router ID** with **router-id 1.1.1.1** command

### Network Command Syntax

On **point-to-point network** => **specify interfaces**

```
R1(config-router)# network network-address wildcard-mask area area-id
```

**Wildcard mask** => **any interface on a router that match network address** => **enable to send and receive OSPF packet**

**area area id** => OSPF area id 

Good practice : **area ID of 0 with single-area OSPFv2** => **easier if network altered to support multiarea OSPFv2**

### Wildcard Mask

*Inverse of the subnet mask*

![[Pasted image 20231115161123.png]]
### Configure OSPF Using the network Command

![[Pasted image 20231115162930.png]]

**Any active interface configured with IPv4 address belonging to that network participate OSPFv2 routing process**

```
R1(config)# router ospf 10
R1(config-router)# network 10.10.1.0 0.0.0.255 area 0
R1(config-router)# network 10.1.1.4 0.0.0.3 area 0
R1(config-router)# network 10.1.1.12 0.0.0.3 area 0
```

**network 10.1.1.5 0.0.0.0 area 0** => enable G0/0/0 for routing process => OSPFv2 process will **advertise the network on this interface 10.1.1.4/30**

**OSPF based on interface address and quad zero wildcard mask**

```
R1(config)# router ospf 10
R1(config-router)# network 10.10.1.1 0.0.0.0 area 0
R1(config-router)# network 10.1.1.5 0.0.0.0 area 0
R1(config-router)# network 10.1.1.14 0.0.0.0 area 0
```

### Configure OSPF Using ip ospf Command

**Configure OSPF directly on interface instead of using the network command**

```
R1(config)# router ospf 10
R1(config-router)# no network 10.10.1.1 0.0.0.0 area 0
R1(config-router)# no network 10.1.1.5 0.0.0.0 area 0
R1(config-router)# no network 10.1.1.14 0.0.0.0 area 0
R1(config-router)# interface GigabitEthernet 0/0/0
R1(config-if)# ip ospf 10 area 0
R1(config-if)# interface GigabitEhternet 0/0/1
R1(config-if)# ip ospf 10 area 0
R1(config-if)# interface Loopback 0
R1(config-if)# ip ospf 10 area 0
```
### Passive Interface

![[Pasted image 20231115165002.png]]

- **OSPF messages forwarded to OSPF-enabled interfaces

**Loopback interfaces** sending unneeded **messages on a LAN affects the networks in three ways 

- **Inefficient Use of Bandwidth** : Available bandwidth consumed transporting unnecessary messages
- **Inefficient Use of Resource :** All devices on the LAN must process and discard the message
- **Increased Security Risk :** OSPF can be intercepted with packet sniffing software without additional OSPF security configurations => route updates can be modified and sent back to the router => false metrics 
### Configure Passive Interfaces

**Passive-interface** => **prevent transmission of routing messages** 

![[Pasted image 20231115165651.png]]

## OSPF Point-to-Point Networks

**Cisco by default** => **elect DR** and **BDR on Ethernet interfaces**

![[Pasted image 20231115170028.png]]

**R1** <=> **BDR**
**R2** <=> **DR**

- **DR and BDR election process not unnecessary**

![[Pasted image 20231115170402.png]]

**No DR or BDR on the link**

### Loopbacks and Point-to-Point Networks

- **loopbacks** => **additional interfaces** => **simulate networks the equipment can support
- **By default** => advertised **as /32 host routes** => **R1 advertise 10.10.1.0/24 as 10.10.1.1/32 to R2 and R3

![[Pasted image 20231115170708.png]]

**Loopback 0 configured as a point-to-point network**

![[Pasted image 20231115171131.png]]

**R2 receives** => **LAN network address of 10.10.1.0/24

![[Pasted image 20231115171202.png]]

### OSPF Network Types

**OSPF Multiaccess Network**

![[Pasted image 20231115223411.png]]

**OSPF Designated Router**

- **DR** responsible for **collecting and distributing LSAs sent and received**
- **DR** use multicast IPv4 address **224.0.0.5** => to communicate with all others OSPF routers
- **DR** and **BDR** **listen on 224.0.0.6

- **BDR in case DR fails** => **listens passively** and **maintains relationship with all routers**
- **If DR producing Hello packets ==> promotes DR**

- **DROTHER** (all others routers) => use multicast address **224.0.0.6** to communicate with **DR and BDR**

***R1 send LSAs to DR and BDR

![[Pasted image 20231115224030.png]]

**DR send LSA to all routers

![[Pasted image 20231115224048.png]]

### OSPF Multiaccess Reference Topology

![[Pasted image 20231115224247.png]]
### Verify OSPF Router Roles

**R1 DROTHER**

![[Pasted image 20231115224455.png]]

**R2 BDR**

![[Pasted image 20231115224547.png]]

**R3 DR**

![[Pasted image 20231115224623.png]]

### Verify DR/BDR Adjacencies

- **show ip opsf neighbor**

**State of neighbors in multiaccess networks**

- **FULL/DROTHER** : DR or BDR router adjacent with non-DR or BDR router => exchange Hello packets, update, queries, replies and acknowledgment

- **FULL/DR** : router adjacent with DR neighbor => exchange Hello packet,s updates, queries, replies and acknowledgments

- **FULL/BDR** : router adjacent with indicated BDR neighbor => exchange Hello packets, updates, queries, replies and acknowledgments

- **2-WAY/DROTHER** : non-DR or BDR router with another non-DR or BDR router => exchange Hello packets

![[Pasted image 20231115224247.png]]

**R1 Adjacencies**

![[Pasted image 20231116101114.png]]

**R2 Adjacencies**

![[Pasted image 20231116101216.png]]

**R3 Adjacencies**

![[Pasted image 20231116101255.png]]

### Default DR/BDR Election Process

=> **router with highest interface priority elect DR** => **second highest interface priority elect BDR**

=> **priority between 0 - 255** => **priority to 0 means cannot elected as DR nor BDR** 

=> **default priority 1** 

==> **If interface priorities equal** => **router with highest router ID** => **elect DR** =>**second with highest router ID elect BDR**

=> **Election process takes few seconds**

=> **OSPF DR and BDR elections are not pre-emptive** => **if new router with higher priority or higher router ID added to the network => does not take over the DR or BDR role** because roles have **already been assigned**

### DR Failure and Recovery

Remains DR until :

- **DR fails**
- **OSPF process on the DR fails or is stopped**
- **Multiaccess interface on the DR fails or shutdown**

**BDR become DR even if another DROTHER with higher priority or router ID is added to the network**

**After BDR is promoted to DR** => **new BDR election occurs** => DROTHER with highest priority or router ID is elected

**R3 Fails**

![[Pasted image 20231116102454.png]]

**R3 Re-Joins Network**

![[Pasted image 20231116102538.png]]

**R4 Joins Network**

![[Pasted image 20231116102602.png]]

**R2 Fails**

![[Pasted image 20231116102627.png]]

**In large network** => **instead of relying** on the **router ID** => **better to control election** with interface **priorities**

Command : **ip ospf priority** value 

**Value range from 0 to 255**

**255 will make the router DR**
### Configure OSPF Priority

```
R1(config)# interface GigabitEthernet 0/0/0
R1(config-if)# ip ospf priority 255
R1(config-if)# end
```

```
R3(config)# interface GigabitEthernet 0/0/0
R3(config-if)# ip ospf priority 0
R3(config-if)# end
```

**To clear OSPF process on R1**

![[Pasted image 20231116103126.png]]

![[Pasted image 20231116103339.png]]

### Cisco OSPF Cost metric

**Higher bandwidth** => **Lower cost**

**Formula** :

*Cost = 100,000,000 bps / interface bandwidth in bps

![[Pasted image 20231116110058.png]]

**Value must be an integer** 

=> **FastEthernet**, **Gigabit Ethernet** and **10 Gigabit Ethernet (10 Gig) share the same cost**

- Adjust with reference bandwidth => **auto-cost reference-bandwidth** 
- Manually set OSPF cost => **ip ospf cost** 

### Adjust the Reference Bandwidth

**Apply configure all routers in the OSPF routing domain**

```
R1(config-router)# auto-cost reference-bandwidth Mbps
```

Reference bandwidth adjusted to the faster links

![[Pasted image 20231116115652.png]]

![[Pasted image 20231116115730.png]]

### OSPF Accumulates costs

![[Pasted image 20231116115810.png]]

**Cost to reach 10.10.2.0/24** => 11 because R2 Cost = 10 and loopback default cost = 1 => 10 +1 = 11

![[Pasted image 20231116115910.png]]

### Manually Set OSPF Cost Value

**Cost value manipulated to influence route chosen** => here we can see load balancing to 10.1.1.8/30 network

![[Pasted image 20231116120125.png]]

- **Traffic go through R2** and use **R3 as a backup route**

![[Pasted image 20231116120336.png]]

```
R1(config)# interface g0/0/1
R1(config-if)# ip ospf cost 30
R1(config-if)# interface lo0
R1(config-if)# ip ospf cost 10
R1(config-if)# end
```

![[Pasted image 20231116120434.png]]

### Test Failover to Backup Route

If link between R1 and R2 goes down ?

![[Pasted image 20231116120643.png]]

### Hello Packet Intervals

**Multicast address 224.0.0.5 (all OSPF routers)** => **every 10 seconds** (default value)

**Cisco default of 4 times the Hello interval for Dead interval**

**=> if Dead interval expires => OSPF removes that neighbor form (LSDB)

=> **Flood LSDB information about down neighbor to all OSPF-enabled interfaces**

![[Pasted image 20231116121220.png]]

### Verify Hello and Dead Intervals

![[Pasted image 20231116121259.png]]

**See the Dead Time counting down**

![[Pasted image 20231116121326.png]]

**Modify OSPFv2 Intervals**

**Change OSPF timer, routers detect network failure in less time**

=> **Increases traffic** => **sometimes the need for quick convergence is more important than the extra traffic it creates

![[Pasted image 20231116121421.png]]

**no ip ospf hello-interval** and **no ip ospf dead-interval** => reset to default timer

**Hello interval must be the same between 2 routers**

![[Pasted image 20231116121747.png]]

![[Pasted image 20231116121825.png]]

### Propagate a Default Static Route in OSPFv2

- Router located **between OSPF routing domain and non-OSPF network** => **autonomous system boundary router (ASBR)**

![[Pasted image 20231116135719.png]]

On ASBR :

- default static route => **ip route 0.0.0.0 0.0.0.0 next-hop-address | exit-intf
- **default-information originate** => R2 to be the source of the default route information and propagate the default static route in OSPF updates

![[Pasted image 20231116135935.png]]

### Verify the Propagated Default Route

**R2 Routing Table**

![[Pasted image 20231116140115.png]]

**R1 Routing Table**

![[Pasted image 20231116140132.png]]

**R3 Routing Table**

![[Pasted image 20231116140156.png]]

### Verify OSPF Neighbors

- **show ip interface brief**
- **show ip route**
- **show ip ospf neighbor**
- **show ip protocols**
- **show ip ospf**
- **show ip ospf interface**

![[Pasted image 20231116140809.png]]

![[Pasted image 20231116140858.png]]

**Neighbor ID** : Router ID of the neighboring router

**Pri** : OSPFv2 priority of the interface => Value use in DR and BDR election

**State** : FULL means router and neighbor have identical OSPFv2 LSDBs <=> 2WAY means no DR or BDR
The dash indicates that no DR or BDR is required

**Dead Time** : amount of time remaining waits to receive OSPFv2 Hello packet before declaring the neighbor down

**Address** : IPv4 address of the interface of the neighbor 

**Interface** : Interface on which the router has formed adjacency with the neighbord


Not form OSPFv2 adjacency if => 

- **Subnet mask don't match** => **Separate Network**
- **OSPFv2 Hello or Dead Timers do not match**
- **OSPFv2 Network Types do not match**
- **Missing or incorrect OSPFv2 network**

### Verify OSPF Protocol Settings

![[Pasted image 20231116141531.png]]
### Verify OSPF Process Information

![[Pasted image 20231116141641.png]]

### Verify OSPF Interface Settings

![[Pasted image 20231116141721.png]]

- **Interfaces are participating in OSPF**
- **Networks that are being advertised (IP Address/Mask)
- **Cost of each link
- **Network state**
- **Number of neighbors on each link**

![[Pasted image 20231116141803.png]]

# ==III. Network Security Concepts==

**Security Terms**

- **Assets** : Value to the organization => people, equipment, ressource and data
- **Vulnerability** : Weakness in a system => exploited by a threat
- **Threat** : Potential danger to a company's assets, data or network functionality
- **Exploit** : Takes advantage of a vulnerability 
- **Mitigation** : counter-measure to reduces the likelihood or severity of a potential threat or risk
- **Risk** : Likelihood of a threat to exploit a vulnerability of an asset
### Vectors of Networks Attacks

**External and Internal Threats**

![[Pasted image 20231115174935.png]]

Internal user (accidentally or intentionally) :

- **Steal** and **copy confidential data to removable media**, **email**, **messaging software and other media
- **Compromise internal servers** or **network infrastructure devices**
- **Disconnect a critical network connection and cause network outage
- **Connect an infected USB drive into a corporate computer system

Internal threats have potential to cause greater damage than external threats => direct access to the building and infrastructure devices

**Knowledge of the corporate network** => its resources and confidential data


## Data Loss

Data => most valuable asset

Include => **research, development data, sales data, financial data, human resource, legal data, employee data, contractor data and customer data**

Data Loss or Data Exfiltration => intentionally  or unintentionally lost, leaked to the outside world

- **Band damage** and **loss of reputation**
- **Loss of competitive advantage
- **Loss of customers
- **Loss of revenue
- **Litigation / legal action resulting in fines and civil penalties
- **Significant cost and effort to notify affected parties and recover from breach

### Data Loss Vectors

![[Pasted image 20231121123259.png]]

**Data loss Prevention (DLP) controls must be implemented**

# Hacker

- Common term used to describe threat actor

![[Pasted image 20231121123733.png]]

# Evolution of Hackers

- **Since 1960s with phone freaking or phreaking** => **using audio frequencies to manipulate phone systems**
- **At that time => telephone switches used various tones to indicate different functions**

**Hacker mimicking a tone using a whistle => exploit phone switches to make free long-distance calls**

![[Pasted image 20231121144702.png]]

## Cyber Criminals

- **Steal billions of dollars from consumers and businesses**
- **Operate in an underground economy => buy, sell and trade attack toolkits, zero day exploit code, botnet services, banking Trojans, keyloggers and much more
- **Buy and sell private information and intellectual property
- Target => **small businesses and consumers => large enterprises and entire industries

## Hacktivists

*Examples

- Anonymous
- Syrian Electronic Army

=> not well organized => cause significant problems for governments and businesses => rely on fairly basic => freely available tools

## State-Sponsored Hackers

- Create advanced, customized attack code => using undiscovered software vulnerabilities "zero-day"

*Example

- Stuxnet malware => damage Iran's nuclear enrichment capabilities

## Threat Actor Tools

Have :

- **Technique or tool**

=> Tools require less technical knowledge 

![[Pasted image 20231122135927.png]]

![[Pasted image 20231122135939.png]]

![[Pasted image 20231122135958.png]]

## Evolution of Security Tools


**Used to test network and keep its data secure => validate the security**

=> Black hat hacker used them for exploitation

![[Pasted image 20231122140504.png]]
![[Pasted image 20231122140514.png]]

## Attack Types

![[Pasted image 20231122141026.png]]

## Malware 

- **Virus :** Malicious software **executes specific unwanted and often harmful function on a computer

- **Worm** : **Executes arbitrary code and installs copies of itself in the memory** => automatically **replicate itself** and **spread across the network**

- **Trojan Horse Attacks** : **non-self-replicating malware** => often **contains malicious code** to look like something else => **from an infected application of file downloaded

## Viruses and Trojan 

Virus **hides itself to computer code, software or documents on the computer

- alter, corrupt, delete files or erase entire drives
- booting issues and corrupt application
- capture and send sensitive information to threat actors
- access and use email accounts to spread
- Lay dormant until summoned by threat actor

![[Pasted image 20231122141958.png]]

Trojan horse is a program **that looks useful but carries malicious code** => provided free online programs such as computer games

![[Pasted image 20231122142200.png]]
## Other Types of Malware

![[Pasted image 20231122142505.png]]

## Overview of Network Attacks

**Networks susceptible to following types of attacks**

- Reconnaissance Attacks
- Access Attacks
- DoS Attacks

## Reconnaissance Attacks

![[Pasted image 20231122142943.png]]

**Internet Information Queries**

![[Pasted image 20231122143037.png]]

![[Pasted image 20231122143053.png]]

**Performing Ping Sweeps**

![[Pasted image 20231122143120.png]]

**Performing Port Scans**

![[Pasted image 20231122143159.png]]

![[Pasted image 20231122143208.png]]

## Access Attacks

**Exploit known vulnerabilities in authentication services, FTP services and web services**

=> gain entry to web accounts, confidential databases and other sensitive information

**Password Attacks** => password cracking tools

**Spoofing Attacks** => IP spoofing, MAC spoofing and DHCP spoofing

**Others Access attacks** 

- **Trust exploitations** => uses unauthorized privileges to gain access to a system

![[Pasted image 20231122144556.png]]

- **Port redirections** => used compromised system for attacks against other targets

![[Pasted image 20231122144629.png]]

- **Man-in-the-middle attacks** => read or modify the data

![[Pasted image 20231122144647.png]]

- **Buffer overflow attacks** => exploits buffer memory and overwhelms with unexpected values => system inoperable

![[Pasted image 20231122144845.png]]

## Social Engineering Attacks

Manipulate individuals => in-person, using telephone or internet => people's weaknesses

=> rely on people weaknesses 

![[Pasted image 20231122145113.png]]

**Social Engineering Protection Practices**

![[Pasted image 20231122145344.png]]

## DoS and DDoS Attacks

- **Overwhelming Quantity of Traffic** : sends an **enormous quantity of data to that network**, **host** or **application cannot handle**. Transmission and response => **slow down**

- **Maliciously Formatted Packets** : Sends maliciously formatted packet to a host or application and receiver is unable to handle it => run very slowly or crash

### DoS Attack

![[Pasted image 20231122145714.png]]

### DDoS (Distributed Dos) Attack

**Coordinated sources** => zombies => botnet => send traffic 

![[Pasted image 20231122145757.png]]


![[Pasted image 20231122145815.png]]

## IP Vulnerabilities and Threats

**IPv4 and IPv6**

- IP does not validate source IP address in a packet => we can spoofed source IP address => other fields in the IP header can carry their attacks

![[Pasted image 20231122150225.png]]

## ICMP Attacks

-  **Discover which hosts are active**
- **Identify host operating system (OS fingerprinting)**
- **Determine state of a firewall**
- **ICMP used for DoS attacks**

=> **should have strict ICMP access control list (ACL) filtering on the network** edge **to avoid ICMP probing**

![[Pasted image 20231122150641.png]]

## Amplification and Reflection Attacks

![[Pasted image 20231122150842.png]]

## Address Spoofing Attacks

*Spoofing attacks can be non-blind or blind

- **Non-blind spoofing** : threat actor see traffic between host and target => non-blind spoofing determines the state of a firewall and sequence-number prediction => can hijack an authorized session

- **Blind spoofing** : threat actor cannot see the traffic between the host and the target => blind spoofing used in DoS attacks

**MAC address spoofing attacks used => threat actors have access to the internal network

**Threat Actor Spoof a Server's MAC Address**

![[Pasted image 20231122152003.png]]

**Switch Updates CAM Table with Spoofed Address**

![[Pasted image 20231122152100.png]]

**Rogue DHCP server to create an MITM condition**

## TCP Segment Header

![[Pasted image 20231122152619.png]]

## TCP Services

- **Reliable delivery** : TCP incorporates acknowledgments to guarantee delivery => instead relying on upper-layer protocols to detect and resolve errors ==> application layer protocols make use of TCP reliability include HTTP, SSL/TLS, FTP, DNS zone transfers and others

- **Flow control** : TCP implements flow control => rather acknowledge one segment at a time => multiple segments can be acknowledged with a single acknowledgment segment

- **Stateful communication** : TCP stateful communication between two partie occurs during TCP three-way handshake 

**=>**

![[Pasted image 20231122153058.png]]

## TCP Attacks

*Network applications use TCP or UDP ports* => threat actors conduct port scans of target devices to discover which service they offer

**TCP SYN Flood Attack**

![[Pasted image 20231122153435.png]]

**TCP Reset Attack**

**Terminating a TCP Connection**

![[Pasted image 20231122153613.png]]

**TCP reset attack** => terminate TCP communications between two hosts

Threat actor could do a **TCP reset attack and send spoofed packet containing a TCP RST to one or both endpoints** by taking the IP of the server

**TCP Session Hijacking**

**Takes over an already-authenticated host** => **threat actor must spoof IP address** of one host and **predict the next sequence number** and **send an ACK to the other host** => threat could send but not receive

## UDP Segment Header and Operation

- UDP **commonly used by DNS, TFTP, NFS and SNMP**
- Real-time applications => media streaming or VoIP => **connectionless transport layer protocol
- **UDP segment structure smaller than TCP's segment structure**

![[Pasted image 20231122162612.png]]

**Function not provided by the transport layer protocol must be implement elsewhere if required

## UDP Attacks

**UDP not protected by any encryption** => we can add encryption to UDP => but not available by default

=> **changing data in the traffic will alter the 16-bit checksum but the checksum is optional** and **not always used**

**When checksum used =>** threat actor **can create a new checksum based on the new data payload and record it in the header as a new checksum** => we can't see data has been altered because checksum matches

**UDP Flood Attacks**

=> **Send from a spoofed host to a server on the subnet** => Program **will sweep through all known ports trying find closed ports => server reply with an ICMP port unreachable message** => **because many closed ports on the server** => **creates a lot of traffic on the segment** => most of the bandwidth => **result similar to DoS attack

## ARP Vulnerabilities

**Services that IP uses for addressing function such as ARP, DNS and DHCP are also not secure**

**Host broadcast** an ARP Request to other hosts **on the segment to determine the MAC address of a host with a particular IP address 

**Host with matching IP address in the ARP Request sends an ARP Reply**

![[Pasted image 20231122163909.png]]
![[Pasted image 20231122163921.png]]
![[Pasted image 20231122164036.png]]
![[Pasted image 20231122164016.png]]

![[Pasted image 20231122163931.png]]*

**Gratuitous ARP** => **unsolicited ARP Reply** => done **when device first boots up to inform all other devices on the local network of the new device's MAC address** => **other hosts on the subnet store the MAC address and IP address contained in the gratuitous ARP**

=> **any host can claim to be the owner of any IP or MAC** => **poison ARP cache** => **creating MITM attack to redirect traffic**

## ARP Cache Poisoning

**ARP Request**

![[Pasted image 20231122164513.png]]

**ARP Reply**

![[Pasted image 20231122164533.png]]

**Spoofed Gratuitous ARP Replies**

![[Pasted image 20231122164629.png]]

## DNS Attacks

*Domain Name System (DNS) protocol* **defines automated services that matches resource names such as** www.cisco.com => **required numeric network address such as IPv4 or IPv6 address**

**DNS Attacks**

- DNS open resolver attacks
- DNS stealth attacks
- DNS domain shadowing attacks
- DNS tunneling attacks

**DNS Open Resolver Attacks**

=> **publicly open DNS servers such GoogleDNS (8.8.8.8) to provide responses to queries**

=> answer queries from clients outside of its administrative domaine 

![[Pasted image 20231122170748.png]]

**DNS Stealth Attacks**

![[Pasted image 20231123090403.png]]

**DNS Domain Shadowing Attacks**

**Threat actor gathering domain account credentials** in order to **silently create multiple sub-domains to be used during attacks** => **subdomains point to malicious server** **without alerting** actual owner of the **parent domain**

## DNS Tunneling

Threat actor who use DNS tunneling **place non-DNS traffic within DNS traffic**

=> **DNS records are altered**

**How its works**

1. **Command data** is **split into multiple encoded chunks**
2. **Each chunk placed into a lower level domain name label of the DNS query
3. Because **no response from the local or networked DNS for the query** => **request send to ISP's recursive DNS server**
4. **Recursive DNS service** will **forward the query to the threat actor's authoritative name server**
5. **Process repeated until all queries containing the chunks of are sent**
6. **When threat actor's authoritative name server receives DNS queries from infected devices** => **sends responses for each DNS query => contain encapsulated encoded CnC commands
7. **Malware on the compromised host recombines the chunks and executes commands hidden within DNS record**

=> **To stop DNS tunneling** => **filter that inspects DNS traffic** => **attention to DNS queries longer than average with suspicious domain name** => **DNS Solution like Cisco OpenDNS block much of the DNS tunneling traffic by identifying the suspicious domains

## DHCP

**Normal DHCP Operation**

![[Pasted image 20231123091816.png]]

**DHCP Spoofing Attack**

**Rogue DHCP server connected to the network** and **provided false IP configuration to legitimate clients**

- **Wrong default gateway** : IP address of its host to create MITM attack => entirely undetected
- **Wrong DNS server :** pointing user to malicious website
- **Wrong IP address :** invalid IP address and invalid default gateway IP => create a DoS attack on the DHCP client

![[Pasted image 20231123092142.png]]
![[Pasted image 20231123092200.png]]
![[Pasted image 20231123092212.png]]
![[Pasted image 20231123092220.png]]

## Confidentiality, Integrity and Availability

**Many Network Attacks** => **to defend your network**

**CIA information security triad :**

- **Confidentiality** : **Only authorized individuals**, **entities** or **processes can access sensitive information** => require **using cryptographic encryption algorithms such as AES to encrypt and decrypt data**

- **Integrity** : **Protecting data from unauthorized alteration** => **use of cryptographic hashing algorithms such as SHA**

- **Availability :** **Authorized users have uninterrupted access to important resources and data** => **implementing redundant services, gateways and links

![[Pasted image 20231123094114.png]]

# The Defense-in-Depth Approach

**Protecting Against networks Attacks**

![[Pasted image 20231123094244.png]]

**Several security device to protect against TCP/IP threats**

- **VPN :** router used to provide secure **VPN services with corporate sites** and **remote access** support for remote users using **secure encrypted tunnels**

- **ASA Firewall** : dedicated device **provides stateful firewall services** => **ensures internal traffic can go out** and **come back** but **external traffic cannot initiate connections to inside hosts**

- **IPS** : **Intrusion Prevention System (IPS) monitor incoming** and **outgoing traffic** looking **for malware, network attack signatures** => **if it recognizes a threat** => **immediately stop it**

- **ESA/WSA** : **Email Security Appliance (ESA) filters spam** and **suspicious emails** => **web security appliance filter known** and **suspicious internet malware sites** 

- **AAA Server** : Server **contains secure database of who authorized to access** and **manage network devices** => Network devices **authenticate administrative users using this database**

**Firewall Operation**

![[Pasted image 20231123100934.png]]

### Firewall

- Resistance to network attacks
- Transit point between internal corporate networks and external networks => all traffic flows through the firewall
- **Enforces access control policy

**Benefits**

- Prevent exposure of sensitive hosts, resources and applications to untrusted users
- Sanitize protocol flow prevents exploitation of protocols flaws
- Block malicious data from servers and clients
- Reduce security management complexity by off-loading most of the network access control to a few firewalls in the network

**Limitations**

- Misconfigured firewall => single point of failure
- Data from many application cannot be passed through firewalls securely
- Users might proactively search for ways to receive blocked material => exposes the network to potential attack
- Network performance can slow down
- Unauthorized traffic can be tunneled or hidden so it appears as legitimate traffic through the firewall

### IPS

**Defend against fast-moving** and **evolving attacks** 

- Router configured with Cisco IOS IPS software
- Device specifically designed to **provide dedicated IDS or IPS services**
- Network module installed in an **adaptive security appliance (AA), switch or router**

![[Pasted image 20231123101749.png]]

**Patterns using signatures** => **signature is set of rules that IDS or IPS uses to detect malicious activity**

**Signatures can be used to detect severe breaches of security** => **detect common network attacks** 

### Content Security Appliances

**Cisco Email Security Appliance (ESA)**

- **Monitor Simple Mail Transfer Protocol (SMTP)**
- **Detects** and **correlates threats and solutions** by using a worldwide database monitoring system

![[Pasted image 20231123102149.png]]


**Cisco Web Security Appliance (WSA)

- Controlling Web Traffic

- Control how users access the internet => chat, messaging, video and audio => restricted with time and bandwidth limits or blocked

- Blacklisting of URLs, URL-filtering, malware scanning, URL categorization, web application filtering and encryption and decryption of web traffic 

![[Pasted image 20231123102443.png]]

## Cryptography 

### Securing Communications 

**Secure data as it travels across links**

*Four element of secure communications

- **Data Integrity** : Message not been altered using Hash Algorithms (SHA-2 or SHA-3) => MD5 still used but insecure (vulnerabilities in network)

- **Origin Authentication** : Message does actually come from whom it states => hash message authentication code (HMAC)

- **Data Confidentiality** : Authorized users can read the message => if message intercepted can't be deciphered within reasonable time => implemented symmetric and asymmetric encryption algorithm

- **Data Non-Repudiation** : only sender has the unique characteristics or signature for how that message is treated

## Data Integrity

![[Pasted image 20231123104007.png]]

## Hash Functions

*4 well-known hash functions

**MD5 with 128-bit Digest**

Developed by Ron Rivest 1991

Legacy algorithm => should be avoided, used when no better alternatives

![[Pasted image 20231123104406.png]]

**SHA Hashing Algorithm**

**Developed by U.S National Security Agency (NSA) in 1995** => **Similar to MD5 hash** => **160-bit hashed messages** => **slower than MD5** => **have known flaws** => **legacy algorithm**

![[Pasted image 20231123104641.png]]

**SHA-2** 

**Developed by NSA** => **includes SHA-224 (224 bit), SHA-256 (256 bit), SHA-384 (384 bits) and SHA-512 (512 bits)**

- SHA-256, SHA-384 and SHA-512 should be used whenever possible

**SHA-3**

Newest hashing algorithm => introduced by NIST => replacement for SHA-2 family of hashing algorithm

- **SHA-3 includes SHA3-224 (224 bit), SHA3-256 (256 bit), SHA3-384 (384 bit), and SHA3-512 (512 bit)

- SHOULD BE USED when possible

Hash vulnerable to man-in-the-middle attacks 

## Origin Authentication

**HMAC** => Keyed-hash Message Authentication code => uses additional secret key as input to the hash function

HMAC => **calculated using cryptographic algorithm** => **combines cryptographic hash function with secret key**

**Only sender** and **receiver know the secret key** => hash function depends on the input data and secret key

**Two parties share a secret key** => defeats man-in-the-middle => **provides authentication of the data origin**

![[Pasted image 20231124160517.png]]
![[Pasted image 20231124160627.png]]
![[Pasted image 20231124160639.png]]
![[Pasted image 20231124160649.png]]

## Data Confidentiality

**2 classes of encryption => provide data confidentiality**

1. **Symmetric encryption** => Data Encryption Standard (DES), 3DES  <= **Legacy algorithm** and Advanced Encryption Standard (AES)

**Each communicating party knows pre-shared key**

**Data Confidentiality using asymmetric algorithms** => **Rivest, Shamir and Adleman (RSA)** and the public key infrastructure **PKI**

![[Pasted image 20231125181519.png]]

## Symmetric Encryption

**Pre-shared key to encrypt and decrypt data** ==> "secret key" knows by sender and receiver

![[Pasted image 20231125181806.png]]

**Symmetric encryption** => **2 categories** => **block ciphers and stream ciphers

- **Block Ciphers** encrypt data in fixed-sized blocks (64-bit or 128-bit) => **examples** => DES and AES

- **Stream Ciphers** encrypt data one bit or byte at a time in a continuos string => **examples** => RC4 and SEAL

==> **Symmetric encryption algorithms** used with **VPN traffic** => symmetric algorithms **use less CPU resources** than asymmetric encryption algorithms => Encryption and decryption of data is faster

**The longer the key** => **longer it will take for someone to discover the key**

**Most encryption keys between 112 and 256 bits** => **Used minimum key length of 128 bits** => longer key used for more secure communications

![[Pasted image 20231125182808.png]]
## Asymmetric Encryption

**Asymmetric algorithms** => **called public-key algorithms** => **Key used for encryption different from the key used for decryption**

**Asymmetric algorithms** => **public key and private key** => achieve confidentiality, authentication and integrity using this process

![[Pasted image 20231125183412.png]]

**Very long key lengths must be used** => **keys lengths between 512 to 4 096 bits => equal to 2048 bit or greater can be trusted, shortest are considered unreliable**

**Protocol that use asymmetric key algorithms**

- **Internet Key Exchange (IKE)** : Fundamental component of IPsec VPNs
- **Secure Socket Layer (SSL)** : now implemented as IETF standard Transport Layer Security (TLS)
- **Secure Shell (SSH)** :  secure remote access connection to network devices
- **Pretty Good Privacy (PGP)** : computer program provides cryptographic privacy and authentication => increase security of email communications

**Asymmetric algorithms** used in low-volume cryptographic mechanisms ==> **Digital signature** and **Key exchange** 

![[Pasted image 20231126144449.png]]

## Diffie-Hellman

**Asymmetric mathematical algorithm** => identical shared secret key **without having communicated before** => **new shared key never exchanged between sender and receiver** 

=> Key can be used by an encryption algorithm to encrypt traffic between two systems


**DH commonly used :** 

- Data exchanged using IPsec VPN
- Using SSL or TLS VPN
- SSH 

![[Pasted image 20231126145144.png]]

**In this figure : color used instead of complex long numbers to simplify DH key agreement process**

**Arbitrary common color don't need to be kept secret** => yellow

**Secret color never be shared with anyone** => Alice's Secret Red Color / Bob's Secret Blue Color => **secret private key of each party**

**Mix common color** => yellow with secret color => **public color**

Alice send her public color (orange) to Bob
Bob send his public color (green) to Alice

Mix color received and original secret color (Red for Alice and Blue for Bob)

=> brown color represents the **resulting shared secret key between Bob and Alice**

DH used very large numbers in its calculations => DH 1024-bit number equal to decimal number of 309 digits => billion is 10 decimal digits (1 000 000 000) to imagine the complexity of working with not one but 309 digit decimal numbers

=> **Asymmetric key systems are extremely slow**
# ==IV. ACL Concepts==

**ACL** => **series of IOS commands** to **filter packets based** on information found in **packet header**

**By default on router** => **No ACL**

**When ACL applied to interface** => perform **additional task of evaluating all network packets** as they pass trough the interface => determine **if it can be forwarded**

**ACEs** as **"Access Control Entries"** => **list of permit** or **deny statements**

**Packet filtering** => router **compares information within the packet against each ACE**
#### ACLs used to identify traffic with Several task

![[Pasted image 20231128164541.png]]

## Packet Filtering

**Analyzing incoming or outgoing packets** <=> **Forwarding** or **Discarding** based **on criteria** ==> packet filtering occur in L3 or L4

![[Pasted image 20231128165102.png]]

*Cisco routers supports two type of ACLs*

- **Standard ACLs** : Filter at **Layer 3 using source IPv4 address only**
- **Extended ACLs** : filter at **Layer 3 using source** and **destination IPv4 address** => **filter Layer 4 using TCP, UDP ports** and **optional protocol type information**

## ACL Operation

=> **Set of rules to add control for packet** that **enter inbound interfaces**, **packets that relay through the router** and **packet that exit outbound interfaces**

![[Pasted image 20231128165552.png]]

**Do not act on packets that originate from the router itself**

- **Inbound ACL filters packets before** they are routed to the **outbound interface** 

- **Inbound ACL best used to filter packets to an inbound interface**


- **Outbound ACL filters packets after being routed** => **routed to the outbound interface** => **processed through the outbound ACL**

- **Outbound ACL best used ==> same filter applied to packets coming from multiples inbound interfaces before existing the same outbound interface**

When ACL applied to an interface with inbound standard IPv4 ACL

- 1. **Router extracts source IPv4 address**
- 2. **Start at the top of ACL and compare source IPv4 address to each ACE** 
- 3. **When match** => **permitting** or **denying packet** 
- 4. **if source IPv4 address does not match any ACEs in the ACL** => **packet discarded** => **implicit deny ACE automatically applied to all ACLs**

**Last ACE of an ACL is always implicit deny that blocks all traffic** => even though it is **hidden or not displayed in the config**

## Wildcard Mask Overview

=> **IPv4 ACE** **uses 32-bit wildcard mask** to **determine which bits of the address to examine for a match**

- **Wildcard mask bit 0 :** **Match** the corresponding bit value in the address
- **Wildcard mask bit 1 :** **Ignore** the corresponding bit value in the address

![[Pasted image 20231129102717.png]]

## Wildcard Mask Types

**Wildcard to Match a Host**

*Need a wildcard mask consisting of all zeroes (0.0.0.0)

![[Pasted image 20231129102940.png]]

**access-list 10 permit 192.168.1.1 0.0.0.0**

**Wildcard Mask to Match an IPv4 Subnet**

*Reserve of Mask* => **0.0.0.255 permit all hosts in the 192.168.1.0/24

![[Pasted image 20231129103302.png]]

**access-list 10 permit 192.168.1.0 0.0.0.255**

**Wildcard Mask to Match an IPv4 Address Range**

**Permit all hosts on 192.168.16.0/24, 192.168.17.0 to => 192.168.31.0/24**

![[Pasted image 20231129103547.png]]

**access-list 10 permit 192.168.16.0 0.0.15.255**

## Wildcard Mask Calculation

In these exemples => **need of ACE in ACL 10**

Method : **Subtract subnet mask from 255.255.255.255** 

*Permit all users in the 192.168.3.0/24 network

![[Pasted image 20231129104722.png]]

Command : **access-list 10 permit 192.168.3.0 0.0.0.255**


*Permit 14 users in the subnet 192.168.3.32/28 

![[Pasted image 20231129104752.png]]

Command : **access-list 192.168.3.32 0.0.0.15**

*Permit only network 192.168.10.0 and 192.168.11.0*

![[Pasted image 20231129105022.png]]

Command : **access-list 10 permit 192.168.10.0 0.0.1.255**

## Wildcard Mask Keywords

**host** => substitutes for 0.0.0.0 mask (all bit must match the filter)
**any** => substitute for 255.255.255.255 mask (ignore or accept any address)

The **ACL 10 ACE permit only the 192.168.10.10 host** and **ACL 11 ACE permits all hosts** 

```
R1(config)# access-list 10 permit 192.168.10.10 0.0.0.0
R1(config)# access-list 11 permit 0.0.0.0 255.255.255.255
```

**Alternatively** 

```
R1(config)# access-list 10 permit host 192.168.10.10
R1(config)# access-list 11 permit any
```

## Limited Number of ACLs per Interface

Limit on the number of ACLs applied to a router interface => **4 ACLs** 

- **one outbound IPv4 ACL**
- **one inbound IPv4 ACL** 
- **one inbound IPv6 ACL**
- **one outbound IPv6 ACL**

![[Pasted image 20231129110104.png]]

R1 has **two-dual-stacked interfaces** => **require inbound and outbound IPv4 and IPv6 ACLs Applied

![[Pasted image 20231129110718.png]]

## ACL Best Practices

- **Require attention to detail and great care** => **Mistakes can be costly in terms of downtime, troubleshooting efforts and poor network service**

![[Pasted image 20231129110852.png]]

## Standard and Extended ACLs

- **Standard ACLs** : Permit or Deny Packets based on **source IPv4 Address**
- **Extended ACLs :** Permit or Deny Packets based on **source IPv4 Address and destination IPv4 Address**, **protocol type, source and destination TCP or UDP ports and more**


**Standard ACL**

```
R1(config)# access-list 10 permit 192.168.10.0 0.0.0.255
```

**Permit host on source network 192.168.10.0/24 => all others deny

**Extended ACL 100 permits traffic originating from any host on the 192.168.10.0/24 network to any IPv4 network if the destination host port is 80 (HTTP)**

```
R1(config)# access-list 100 permit tcp 192.168.10.0 0.0.0.255 any eq www
```

## Numbered and Names ACLs

### Numbered ACLs

**Number 1 to 99** => **Standard ACL**
**Number 1300 to 1999** => **Expanded Standard ACL range**

**Number 100 to 199** => **Extended ACL**
**Number 2000 to 2699** => **Expanded Extended ACL range**

![[Pasted image 20231129112606.png]]

### Names ACLs 

Use to provide information about the purpose of the ACL

```
R1(config)# ip access-list extended FTP-FILTER
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq ftp
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq ftp-data
```

- **Assign a name to identify the purpose of the ACL**
- **Names can contain alphanumeric characters**
- **Names cannot contain spaces or punctuation**
- **It is suggested that the name be written in CAPITAL LETTERS**
- **Entries can be added or deleted within the ACL**

## Where to Place ACLs

Implemented where it has the greatest impact on efficiency

![[Pasted image 20231129113216.png]]

**As close as possible to the source of the traffic to be filtered** => **undesirable traffic can be deny quickly without crossing network infrastructure**

**Placement of ACL depend on a variety of factors**

![[Pasted image 20231129113401.png]]

## Standard ACL Placement Example

Administrator **prevent traffic originating 192.168.10.0/24 from reaching the 192.168.30.0/24 network

![[Pasted image 20231129113730.png]]

**Two possible interface on R3 to apply the standard ACL**

- **R3 S0/1/1 interface (inbound) :** Deny traffic from 192.168.10.0/24 network => it would also filter traffic to 192.168.31.0/24

- **R3 G0/0 interface (outbound) :** Will not affect other networks that are reachable by R3 => 192.168.10.0/24 network will still able to reach the 192.168.31.0/24 => best interface to place the standard ACL to meet the traffic requirements

## Extended ACL Placement Example



![[Pasted image 20231129115404.png]]

- **R1 S0/1/0 interface (outbound) :** Process all packet leaving R1 including packet from 192.168.10.0/24

- **R1 G0/0/1 interface (inbound) :** Only packets from the 192.168.11.0/24 networks => best solution

# ==V. ACLs for IPv4 Configuration==

## Create an ACL

Suggest :

- **Write specifics policy to be implemented**
- **Add IOS configuration commands to accomplish those tasks**
- **Include remarks to document the ACL**
- **Copy and paste command on device**
- **Test an ACL to ensure that it correctly applies the desired policy**

## Numbered Standard IPv4 ACL Syntax

![[Pasted image 20231201105142.png]]

**no access-list** *access-list* to remove a numbered standard ACL

![[Pasted image 20231201105241.png]]

## Named Standard IPv4 ACL Syntax

![[Pasted image 20231201105915.png]]

To delete an Name Standard ACL : **no ip access-list standard** *access-list-name*

- Alphanumeric and unique

![[Pasted image 20231202144747.png]]

- **Named Standard IPv4 ACL called NO-ACCESS created**
- **Highlighted options**
## Apply a Standard IPv4 ACL

- IPv4 ACL must be **linked to an interface or feature**

![[Pasted image 20231202150803.png]]

- To remove an ACL from an interface **no ip access-group** 
- ACL will still be configured on the router, to remove ACL from router **no access-list** 

## Numbered Standard IPv4 ACL Example

![[Pasted image 20231202151605.png]]

**ACL ACE could be applied outbound to S0/1/0** 

![[Pasted image 20231202151729.png]]

**ACL remarks in running conf**

**Remark not required to enable ACL** => **but strongly suggested for documentation purposes**

**LAN 2 permit to internet**

![[Pasted image 20231202234929.png]]

**Apply ACL 10 to Serial0/1/0 interface**

![[Pasted image 20231202235320.png]]

**ACL 10 permit host 192.168.10.10 and all hosts from LAN 2 to exit the Serial 0/1/0 interface**

![[Pasted image 20231202235601.png]]

**We can see ACL config**

![[Pasted image 20231202235619.png]]

**show ip interface**

![[Pasted image 20231202235726.png]]

## Named Standard IPv4 ACL Example

![[Pasted image 20231202235831.png]]

**PC1 allowed out to the internet**

**ACL called PERMIT-ACCESS**

![[Pasted image 20231203000235.png]]

**ACE permit only host 192.168.10.10 and another ACE permitting all LAN 2 hosts to the internet**

![[Pasted image 20231203001132.png]]

**Apply ACL outbound to the Serial 0/1/0 interface**

![[Pasted image 20231203001339.png]]

**Show access-lists and running-config**

![[Pasted image 20231203001422.png]]

**Show ip interface**

![[Pasted image 20231203001545.png]]

## Modify IPv4 ACLs

### Two Methods to Modify an ACL

- **ACL with multiple ACEs** can be **complex to configure** and may require a **bit of trial to achieve desired filtering result

When modifying an ACL :

- Use Text Editor
- Use Sequence Numbers
## Text Editor Method

We can see an erreur on 19.168.10.10

![[Pasted image 20231203010633.png]]

- **Copy ACL from running-config to text editor
- **Edit error**
- **Remove previously configured ACL
- **Copy paste edited ACL**

![[Pasted image 20231203010742.png]]
## Sequence Numbers Method

**ACL ACE can be deleted or added using ACL sequence numbers** => **Sequence number automatically assigned when ACE is entered**

Error on ACE 10 "19.168.10.10"

![[Pasted image 20231203010802.png]]

- **ip access-list standard** to edit an ACL
- **Cannot overwritten** => **no 10** command => **10 deny host 192.168.10.10**

![[Pasted image 20231203011047.png]]

## Modify a Named ACL Example

**Used sequence number to delete and add ACEs**

![[Pasted image 20231203011204.png]]

- **The order of sequence number is important**
- **In order to deny host 192.168.10.5** we must put ACE = 15 to be **between ACE 10** => deny host 192.168.10.10 and **ACE 20** => permit all the 192.168.10.0 network

![[Pasted image 20231203011619.png]]

ACE 10 before ACE 15 => **Order of the statements in the output reflect the order in which they were entered** 

## ACL Statistics

**Show statistics for each statement that has been matched**

![[Pasted image 20231203012416.png]]

- **To manually track denied packets => configure deny any at the end of the ACL**
- **clear access-list counters**

## Secure VTY Ports with a Standard IPv4 ACL

**ACL can be used to secure remote administrative access to a device using vty lines**

- **Creates ACL to identify which administrative hosts should be allowed remote access**
- **Apply the ACL to incoming traffic on the vty lines**

![[Pasted image 20231203020205.png]]

**in** most commonly used to filter incoming vty traffic 

**out** filters outgoing vty traffic and rarely applied

- **Both named and numbered access lists can be applied to vty lines**
- **Identical restrictions should be set on all the vty lines because user can attempt to connect to any of them** 

## Secure VTY Access Example

![[Pasted image 20231203020729.png]]

**Add IP of the ADMIN to vty lines**

![[Pasted image 20231203020902.png]]

**Only allow SSH**

![[Pasted image 20231203020922.png]]

## Verify VTY Port is Secured

![[Pasted image 20231203021040.png]]

![[Pasted image 20231203021052.png]]

**Show matches**

![[Pasted image 20231203021104.png]]

## Configured Extended IPv4 ACLs

*Extended ACLs can be created as :

- **Numbered Extended ACL**
- **Named Extended ACL** 
## Numbered Extended IPv4 ACL Syntax

![[Pasted image 20231203022000.png]]

**no access-list** *access-list-number* to remove an extended ACL

**Keywords and parameters for extended ACLs**

![[Pasted image 20231203022046.png]]

**Apply extended IPv4 ACL to an interface**

![[Pasted image 20231203022417.png]]

**To remove an ACL from an interface** => **no ip access-group**

**To remove from the router** => **no access-list**

*The order in which the statement are entered during config is the order they are displayed and processed*

## Protocols and Ports

**The four most popular ip protocol options used highlighted

![[Pasted image 20231203140151.png]]

- If an internet protocol is not listed => the ip protocol number could be specified (ICMP protocol is number 1, TCP is 6 and UDP is 17)

**Port Keyword Options**

- **tcp**  
- **udp** 
- **icmp** 

Ports names or number can be specified => port for SSH and HTTPS are not listed because we will have to be specified

![[Pasted image 20231203140803.png]]

## Protocols and Port Numbers Configuration Examples

This 2 different ACE does the same job => filter HTTP traffic

![[Pasted image 20231203140942.png]]

Configure **specified port number** => **SSH as port number 22 or HTTPS as port number 443** 

![[Pasted image 20231203141120.png]]

## Apply a Numbered Extended IPv4 ACL

![[Pasted image 20231203141202.png]]

- **ACL permit HTTP and HTTPS from the 192.168.10.0 network to go to any destination
- **Extended ACL commonly applied close to the source => here applied inbound R1 g0/0/0**

![[Pasted image 20231203141335.png]]

## TCP Established Extended ACL

**TCP*** perform basic stateful firewall services

![[Pasted image 20231203141450.png]]

- **ACL 110** filter traffic from the inside private network
- **ACL 120** filter traffic coming into the inside private network from the outside public network

![[Pasted image 20231203141814.png]]

**established** allow only response to traffic that **originates from the 192.168.10.0/24 network to return to that network** => match occurs if the returning TCP segment has the ACK or reset (RST) flag bits set

- Without **established** parameter in the ACL => client could send traffic to a web server and receive traffic returning from the web server => all traffic would be permitted

## Named Extended IPv4 ACL Syntax

![[Pasted image 20231203143240.png]]

**Alphanumeric, case sensitive and must be unique**

![[Pasted image 20231203143312.png]]

## Named Extended IPv4 ACL Example

We will create 2 ACL

- **SURFING** : Permit inside HTTP and HTTPS traffic to exit to the internet
- **BROWSING** : Permit returning web traffic to the inside hosts while all other traffic exiting the R1 g0/0/0 interface is implicitly denied

![[Pasted image 20231203143524.png]]

![[Pasted image 20231203143748.png]]

## Edit Extended ACLs

**Use a text editor when many changes are required**

![[Pasted image 20231203144106.png]]

**Remove original statement and add replacing the original statement** 

![[Pasted image 20231203144205.png]]

**To verify** => **show access-lists**

![[Pasted image 20231203144216.png]]

## Another Named Extended IPv4 ACL Example

2 ACL created :

- **PERMIT-PC1** : Only permit PC1 TCP access to he internet and deny all other hosts in the private network
- **REPLY-PC1** : Only permit specified returning TCP traffic to PC1 implicitly deny all other traffic

![[Pasted image 20231203144538.png]]

- **PERMIT-PC1** ACL => **permit PC1 192.168.10.10** to **access to FTP (20 and 21)**, **SSH (22)**, **Telnet (23)**, **DNS (53)**, **HTTP (80)** and **HTTPS (443) traffic** => **DNS (53) permitted for both TCP and UDP**

- **REPLY-PC1** ACL => permit return traffic to PC1

*Factor to consider when applying an ACL :

- device to apply it on
- interface to apply it on
- direction to apply it

![[Pasted image 20231203145144.png]]

## Verify Extended ACLs

**show ip interface** => verify ACL apply to interface and direction

![[Pasted image 20231203145258.png]]

**show access-lists** => show ACL work as expected and displays statistics, IOS displaying keyword even though port numbers were configured

![[Pasted image 20231203145356.png]]

**show running-config**

![[Pasted image 20231203145537.png]]
# ==VI. NAT for IPv4==

- IPv4 run out of IP address => **32 bit numbers** => **4 billion unique IPv4** => **IoT**
- IPv6 was developed to **counter this problem**
- **Most networks are IPv4 only** or a **combinaison of IPv4 and IPv6**

=> NAT (Network Address Translation) developed

## IPv4 Private Address Space

- **Not enough public IPv4 addresses to assign a unique address to each device connected to the internet**

*Implemented private IPv4 addresses defined in RFC 1918*

![[Pasted image 20231209160719.png]]

- Used to **communicate locally within organization**
- **Private address must be translated to public address** => **NAT**

![[Pasted image 20231209161054.png]]

**Without NAT** => **exhaustion of IPv4 address space** would have occurred **before year 2000**
## What is NAT

Allowing network **to use private IPv4 addresses internally** and **provide translation to a public address only when needed**

- Hides internal IPv4 addresses from outside networks
- **NAT-enabled routers** can be configured **with one or more valid public IPv4 addresses** => known as the **NAT pool**
- **NAT operates at the border of a stub network**

![[Pasted image 20231210174735.png]]

- **Translating internal private address of the device** to **a public, outside, routable address**

## NAT Terminology

- **Inside network** is the set of networks that is subject to translation 
- **Outside network** refers to all other networks

NAT includes four types of addresses :

- **Inside local address**
- **Inside global address**
- **Outside local address**
- **Outside global address**

**Inside address :** Address of the device which is being translated by NAT
**Outside address :** Address of the destination device

**Local address** : local address that appears on the inside portion of the network
**Global address :** global address is any address that appears on the outside portion of the network

![[Pasted image 20231210175827.png]]

- **Inside local address** : address of the ource from inside the network => typically private IPv4 address => 192.168.10.10 assigned to PC1

- **Inside global address :** Address source from the outside network => typically globally routable IPv4 address => here PC1 traffic to web server 209.165.201.1 => R2 translates IPv4 source address from 192.168.10.10 to 209.165.200.226 => in NAT terminology inside local address of 192.168.10.10 is translated to the inside global address of 209.165.200.226

- **Outside global address** : Globally routable IPv4 assigned to a host on the internet => web server reachable at IPv4 address of 209.165.201.1 => Most often outside local and outside global addresses are the same

- **Outside local address** : address of the destination as seen from the inside network => PC1 sends traffic to the web server at the IPv4 address 209.165.201.1 while uncommon this address could be different than the globally routable address of the destination

## Type of NAT

### Static NAT

- **one-to-one mapping of local and global addresses

![[Pasted image 20231210182300.png]]

- Useful for **web server** and **device** that must have a **consistent address accessible from the internet**
- Useful when **administration ssh to gain access to the inside global address of Svr1** (209.165.200.226), **R2 translates this inside global address to the inside local address 192.168.10.10** and connect the session to Svr1

### Dynamic NAT

- **Uses pool of public addresses and assigns them on a first-come, first-served basis

![[Pasted image 20231210182707.png]]

### Port Address Translation

- **PAT** => also known as **NAT overload** => **map multiple private IPv4 addresses to a single public IPv4 address of a few address** => **most home routers do**

- **ISP assigns one address to the router but yet several members of the household can simultaneously access the internet** => **most common form of NAT for both home and enterprise

- **Each private address is tracked by a port number** => device initiates TCP/IP session => **generates TCP or UDP source port value** or a **assigned query ID for ICMP to uniquely identify the session

- **NAT router** uses source port number to uniquely identify the specific NAT translation

![[Pasted image 20231210183341.png]]


![[Pasted image 20231210183246.png]]

**R2 processes each packet** => **uses port number 1331 and 1555 to identify the device from which the packet originated** => **source address (SA) is the inside local address with TCP/UDP assigned port number added** => **destination address (DA) is the outside global address with service port number added** => **service port number is 80 which is HTTP**

R2 **translated inside local address** => to **inside global address with the port number added**, **destination address is not changed** but is now **referred to as outside global IPv4 address** => When web **server replies => path is reversed**

## Next Available Port

**If original source port already used** => **PAT assigns** the **first available port number starting** from the beginning of the appropriate **port group 0 - 511**, **512 - 1023** or **1024 to 65535**

- **When no more ports available** and there is **more than one external address in the address pool** => **PAT moves to the next address to try to allocate the original source port** (process continues until there are no more available ports or external IPv4 addresses) 

![[Pasted image 20231210185449.png]]

- **First 420 ports in the range 1024 to 65535 where used** so the **next available port number is 1445**

## NAT and PAT Comparison

![[Pasted image 20231210185819.png]]

**NAT Table**

![[Pasted image 20231210190016.png]]

**PAT Table**

![[Pasted image 20231210190028.png]]

## Packet without a Layer 4 Segment

- **ICMPv4 handled differently by PAT** => **ICMPv4 query messages, echo requests and echo replies include a Query ID**

- Query ID is incremented with each echo request send => PAT use Query ID instead of Layer 4 port number to track ICMP traffic

## Advantages of NAT

- **NAT conserves legally registered addressing scheme** => **few external addresses are required to support many internal hosts

- **Increase flexibility ==> Multiple pools, backup pools and load-balancing pools** => **reliable public network connections**

- **Organization could change ISPs and don't need to change IP of inside clients**

- **Using RFC 1918 IPv4 addresses** => **NAT hides IPv4 addresses of users and other devices** => some people consider this a **security feature but expert agree that NAT does not provide security** => **stateful firewall provides security on the edge of the network**


## Disadvantage of NAT


- **Increase forwarding delays (translation of each IPv4 address within the packet headers)** => first packet always process-switched going through the slower path

- Router **look every packet** and **must alter IPv4 header and TCP or UDP header**=> **IPv4 header checksum must be recalculated each translation** =>  Remaining packets go through fast-switched path if a cache entry exists, otherwise they are delayed

-  **Forwarding delays becomes more of an issue as the pools of public IPv4 addresses for ISPs**

- **Many ISPs assign private IPv4 address instead of public address** => ISP perform NAT again translating its private IPv4 addresses to one of its limited number of public IPv4 addresses => process of two layer of NAT translation is CGN **Carrier Grade NAT**

- **End-to-end addressing lost** => Application rely on this like digital signatures

- **Difficult to trace packet** => troubleshooting challenging

- **Complicates use of tunneling protocols such as IPsec => NAT modifies values in the headers causing integrity checks to fail**

## Static NAT Scenario



![[Pasted image 20231210193722.png]]

## Configure Static NAT

**Step 1**

- **Create mapping between the inside local address** and the **inside global addresses**
- **192.168.10.254 inside local address and 209.165.201.5 inside global address**

```
R2(config)# ip nat inside source static 192.168.10.254 209.165.201.5
```

**Step 2**

- **Interfaces participating in the translation as inside or outside relative to NAT** 

```
R2(config)# interface serial 0/1/0
R2(config-if)# ip address 192.168.1.2 255.255.255.252
R2(config-if)# ip nat inside
R2(config-if)# exit
R2(config)# interface serial 0/1/1
R2(config-if)# ip address 209.165.200.1 255.255.255.252
R2(config-if)# ip nat outside
```

- **R2 Serial 0/1/0** configured inside local IPv4 address 192.168.10.254 translated and forwarded towards the outside network

- **R2 Serial 0/1/1** are addressed to inside global IPv4 address 209.165.201.5 translated to inside local address 192.168.10.254 and forwarded to the inside network

## Analyze Static NAT

![[Pasted image 20231210231339.png]]

## Verify Static NAT

**show active NAT translations

![[Pasted image 20231210231905.png]]

**During an active session**

![[Pasted image 20231210232124.png]]

**Show ip nat statistics** => total number of active translations, NAT configuration parameters, number of addresses in the pool and number of addresses that have been allocated

![[Pasted image 20231210232204.png]]

We can also **clear ip nat statistics**

![[Pasted image 20231210232308.png]]

## PAT

**ISP allocates a single public IPv4 address**

![[Pasted image 20231211130140.png]]

## Configure PAT to Use a Single IPv4 Address

- To **use single IPv4 address** => add **overload** to **ip nat inside source** command

```
R2(config)# ip nat inside source list 1 interface serial 0/1/1 overload
R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255
R2(config)# interface serial0/1/0
R2(config-if)# ip nat inside
R2(config-if)# exit
R2(config)# interface Serial0/1/1
R2(config-if)# ip nat outside
```

- **Multiple host can use the same public IPv4 address to access the internet**

- **All hosts from network 192.168.0.0/16 matching ACL 1** that send traffic through router R2 will be **translated to IPv4 address 209.165.200.225 IP** (IPv4 address of interface s0/1/1)

- **Traffic identified by port number in NAT table** ==> because **overload** configure

## Configure PAT to Use an Address Pool

- ISP may allocate **more than one public IPv4 address to an organization** => use **pool of IPv4 public addresses for translation**

```
R2(config)# ip nat pool NAT-POOL2 209.165.200.226 209.165.200.240 netmask 255.255.255.224
R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255
R2(config)# ip nat inside source list 1 pool NAT-POOL2 overload
R2(config)# 
R2(config)# interface serial0/1/0
R2(config)# ip nat inside
R2(config)# interface serial0/1/1
R2(config)# ip nat outside
R2(config)# end
```

- **ACL permit 192.168.0.0/16 to be translated** 

## Analyze PAT - PC to Server


![[Pasted image 20231211131905.png]]

![[Pasted image 20231211131921.png]]

## Analyze PAT - Server to PC


![[Pasted image 20231211132332.png]]
![[Pasted image 20231211132351.png]]

## Verify PAT

- **show ip nat translations** 

- **We can see translations from two different hosts to different web servers**

![[Pasted image 20231211132638.png]]

- **show ip nat statistics** => number and type of active translations, NAT configuration parameters, number of addresses in the pool and how many have been allocated

![[Pasted image 20231211132826.png]]

## NAT64

- **NAT64 provide protocol translation between IPv4 and IPv6**

- IPv6 include its **own IPv6 private address space**, **unique local adresses (ULAs)** => are **similar to RFC 1918 private addresses in IPv4 but different purpose**

=> **ULA** addresses **don't provide additional IPv6 address space** and **don't provide a level of security**


![[Pasted image 20231211194415.png]]

=> **to aid to move from IPv4 to IPv6** => **IETF developed several transition techniques** to accommodate a variety of IPv4-to-IPv6 scenarios => **dual-stack**, **tunneling** and **translation**


- **Dual-stack** ==> **device run protocols associated with both IPv4 and IPv6**

- **Tunneling for IPv6** => process of **encapsulating an IPv6 packet inside an IPv4 packet** => **IPv6 packets can be transmitted over IPv4-only network**

*NAT for IPv6 => temporary mechanism to assist migration from IPv4 to IPv6

- **Several types of NAT for IPv6** (NAT-PT) Network Address Translation-Protocol Translation **by IETF but NAT64 is the replacement**
# ==VII. WAN Concepts==

## LANs and WANs

- **At work** or **home** ==> **use Local Area Networks (LANs)** => **limited small geographical area**

- **WAN** => required to **connect beyond boundary of the LAN** => **operates beyond geographic scope of a LAN**

![[Pasted image 20231212175027.png]]

**Table between LANs and WANs**

![[Pasted image 20231212175054.png]]

## Private and Public WANs

**WAN built by different types of organizations**

- Organizations that want to **connect users in different locations**
- ISP that want to **connect customers to the internet**
- ISP or telecommunications that want to **interconnect ISPs**

**Private WAN** => **connection dedicated to a single customer** => provides :

- **Guaranteed service level**
- **Consistent bandwidth**
- **Security**

**Public WAN** => provided by ISP or telecommunications service provider using the internet 

- **Service level vary**
- **Bandwidth vary**
- **Shared connections not guarantee security**

## WAN Topologies

**Example :** video conference call between the user in New York and Japan would be **a logical point-to-point connection**

**Implemented using following logical topology designs**

- **Point-to-Point Topology
- **Hub-and-Spoke Topology**
- **Dual-homed Topology**
- **Fully Meshed Topology**
- **Partially Meshed Topology**
### Point-to-Point Topology

*Employs point-to-point circuit between two endpoints

![[Pasted image 20231212180051.png]]

- **Often involve dedicated** => **leased-line connections from the corporate edge point to the provider networks**
- Involve **Layer 2 transport service through the service provider networks**
- **Point-to-point connection is transparent to the customer network** => **seems like a direct physical link between two endpoints**
- **Can be expensive**

### Hub-and-Spoke Topology

*Enable a single interface on the hub router to be shared by all spoke circuits*

![[Pasted image 20231212180449.png]]

- Spoke routers can be **interconnected through the hub router using virtual circuits and routed subinterfaces**
- Hub-and-spoke topology is a **single-homed topology** => **one hub router and all communication must go through it**
- Spoke routers can **only communicate with each other through the hub router**
- **Hub router** => **single point of failure** => if hub fail => **inter-spoke communication fails**

### Dual-homed Topology

*Dual-homed topology provides redundancy*

![[Pasted image 20231212181014.png]]

*Advantages

- **Offer enhanced network redundancy, load balancing, distributed computing and processing** => **implement backup service provider connections**

*Disadvantage*

- **More expensive to implement than single-homed topologies**
- Require **additional networking hardware** => **additional routers and switches**
- **Additional** and **more complex configurations**

## Fully Meshed Topology

*Used of multiple virtual circuits to connect all sites*

![[Pasted image 20231212182206.png]]

- **Most fault-tolerant topology** => **if SITE B lost connectivity to site A** => it could **send the data through either site C or site D**

## Partially Meshed Topology

*Connect many but not all sites*

![[Pasted image 20231212182445.png]]

- **Here A, B and C fully meshed**
- **D must connect A to reach B and C**

## Carrier Connections

- Organization **signs a service level agreement (SLA)**
- SLA outlines **expected services relating to the reliability and availability of the connection**
- Service provider **may or may not be the actual carrier**

*Carrier owns and maintains physical connection and equipment between the provider and the customer

- Organization choose **either a single-carrier or dual-carrier WAN connection**
### Single-Carrier WAN Connection

When organization connect **only one service provider**

- **SLA negotiated** between **organization and service provider**

*Disadvantage*

- **Carrier connection and service provider are both single points of failure** => **connectivity to the internet would be lost if carrier link** or **provider router failed**

![[Pasted image 20231212183126.png]]

### Dual-Carrier WAN Connection

- **Provides redundancy and increases network availability** 
- **Organization negotiates separates SLAs** with **two different service providers**
- Organization should ensure that the **two providers each use a different carrier**
- **More expensive**
- Second link can be used for **redundancy as a backup link** => also be used to **improve network performance and load balance internet traffic**

![[Pasted image 20231212183624.png]]

## Evolving Networks

**Network requirement** of a company **can change dramatically** as the **company grows over time**

Network : **meet the day-to-day operational needs of the business** and **adapt/grow as the company changes**

*Example of evolving company*
### Small Network

- **15 employees in small office**
- Use **single LAN connected to a wireless router** => inter through **common broadband service** => **Digital Subscriber Line (DSL)** => **supplied by local telephone service provider** 

![[Pasted image 20231212184426.png]]

### Campus Network

- **Within few years** => **company grew** with **several floors of a building**
- Company now required a **Campus Area Network (CAN)** => **interconnects several LANs** within **limited geographical area**
- **Various department connecting to multiple switches in a campus network environment**
- Include **servers for email**, **data transfer**, **file storage**, **web-based productivity tools and applications** 

![[Pasted image 20231213084450.png]]

- Firewall **secures internet access to corporate users** => business require **in-house IT staff to support and maintain network**

### Branch Network

- Company expanded => **add branch site, remote and regional sites in others cities**
- Require **MAN (Metropolitan Area Network)** to interconnect sites => **MAN larger than LAN** but **smaller than WAN**

![[Pasted image 20231213084701.png]]

- Used of **private dedicated lines through their local service provider**
- Offices in other cities and countries **require service of a WAN** or **may use internet services** to connect distant locations 
- **Internet** introduces security and privacy issues that the IT team must address

### Distributed Network

- **SPAN Engineering** has been in business for 20 years, grown to thousands of employees **distributed in offices worldwide**

- To reduce network costs => **encouraged teleworking**, **virtual teams using web-based applications, including web-conferencing, e-learning and online collaboration tools** to increase productivity

![[Pasted image 20231213085317.png]]

- **Site-to-site** and **remote access Virtual Private Networks (VPNs)** to connect easily and securely with employees around the world
## WAN Standards

- **Telegraph system** was the **first large-scale WAN** => **followed by radio** => **telephone system** => **television** => now **data networks** 

*Modern WAN standards :

- **TIA/EIA** : Telecommunications Industry Association and Electronic Industries Alliance
- **ISO** : International Organization for Standardization
- **IEEE** : Institute of Electrical and Electronics Engineers

Most WAN standards **focus on physical layer (OSI Layer 1)** and **data link layer (OSI Layer 2)**

![[Pasted image 20231213090324.png]]

## Layer 1 Protocols

- Describe the **electrical, mechanical and operational component needed to transmit bits over a WAN**

Service providers use **high-bandwidth optical fiber media to span long distances** using following standard Layer 1 :

- **Synchronous Digital Hierarchy (SDH)
- **Synchronous Optical Networking (SONET)
- **Dense Wavelength Division Multiplexing (DWDM)**

SDH and SONET provide **same services** => **transmission capacity can be increased using DWDM technology**

## Layer 2 Protocols

Define **how data will be encapsulated into a frame**

Several Protocols

- **Broadband (DSL and Cable)**
- **Wireless**
- **Ethernet WAN (Metro Ethernet)**
- **Multiprotocol Label Switching (MPLS)**
- **Point-to-Point Protocol (PPP) less used**
- **High-Level Data Link Control (HDLC) less used
- **Frame Relay (legacy)
- **Asynchronous Transfer Mode (ATM) (legacy)

## Common WAN Terminology

**WAN physical layer** => physical connections **between the company network and service provider network

![[Pasted image 20231213092943.png]]

![[Pasted image 20231213093534.png]]
![[Pasted image 20231213093553.png]]
## WAN Devices

![[Pasted image 20231213094111.png]]

![[Pasted image 20231213094213.png]]
![[Pasted image 20231213094227.png]]

## Serial Communication

- Serial communication **transmit bits sequentially over a single channel**
- Parallel communication **simultaneously transmit several bits using multiple wires**

![[Pasted image 20231213094715.png]]

- Parallel connection **transfers data eight times faster than a serial connection** => **Synchronisation problems**

As the cable length increases => **synchronization timing between multiple channels become more sensitive to distance**

- **Parallel communication limited to very short distances only** => **copper media** limited to **less than 8 meters

![[Pasted image 20231213110112.png]]

- Parallel communication **not viable for WAN communication** => its a viable solution in **data centers where distances between servers and switches are short

=> **Cisco Nexus switches in data centers support parallel optics solutions** to transfer more data signals and **achieve higher speeds 40 Gbps and 100 Gbps**

## Circuit-Switched Communication

Circuit-switched network => **established dedicated circuit or channel between endpoints** before users can communicate

![[Pasted image 20231213110502.png]]

Circuit switching **dynamically establishes a dedicated virtual connection** through the service provider network **before voice or data communication can start**

*Example : User makes a telephone call using a landline, the number called is used by the provider equipment to create a dedicated circuit form the caller to the called party*

Two most common types of circuit-switched WAN technologies are the **public switched telephone network (PSTN)** and the **legacy Integrated Services Digital Network (ISDN)**

## Packet-Switched Communications

**Most commonly implemented** in **Network communication** => packet-switching **segments traffic data intro packets that are routed over a shared network**

Packet-switched network **don't require a circuit to be established** => **allow many pairs of nodes to communicate over the same channel**

![[Pasted image 20231213111535.png]]
![[Pasted image 20231213111548.png]]

- Packet switching **less expensive and more flexible** than circuit switching => susceptible to **delay (latency)** and **variability of delay (jitter)**, **modern technology allows satisfactory transport of voice and video communications**

Common types of packet-switched WAN technologies are **Ethernet WAN (Metro Ethernet), Multiprotocol Label Switching (MPLS), as well as Frame Relay and Asynchronous Transfer Mode (ATM) which are legacy types**

## SDH, SONET and DWDM

- Service provider networks **use fiber-optic infrastructures to transport user data between destinations**

- Fiber-optic cable **is superior to copper cable for long distance transmissions** due to its much **lower attenuation and interference

Two optical fiber OSI layer 1 standards available to service providers

- **SDH** : Synchronous Digital Hierarchy (SDH) is a global standard for transporting data over fiber-optic cable
- **SONET** : Synchronous Optical Networking (SONET) is the North American standard that provides the **same services as SDH

SDH/SONET => define **how to transfer multiple data, voice and video communications over optical fiber using lasers or light-emitting diodes (LEDs) over great distances** 

**Dense Wavelength Division Multiplexing (DWDM)** => increases **data-carrying capacity of SDH and SONET** by **simultaneously sending multiple streams of data (multiplexing)** using **different wavelengths of light**

![[Pasted image 20231213133535.png]]

## Traditional WAN Connectivity Options

- LAN **appeared in 1980s**
- Need of **interconnect with other locations** => need to **connect to the local loop of a service provider**
- Use **of dedicated lines** or using **switched services from a service provider**

![[Pasted image 20231213141236.png]]
## Common WAN Terminology

- When permanent dedicated connections were required => **point-to-point using copper media was used** to provide pre-established **WAN communications path from the customer premises to the provider network** 

=> Point-to-point lines could **be leased** from a service provider => **leased lines** => organization pays a **monthly lease fee to a service provider to use the line**

- Leased lines => **existed since early 1950s** => different names such as **leased circuits, serial link, serial link, point-to-point link and T1/E1 or T3/E3 lines**

=> **different fixed capacities** => priced based on **bandwidth required and distance between the two connected points**

*Two systems used to define the digital capacity of a copper media serial link

- **T-carrier** : Used in North America, T-carrier provides **T1 links supporting bandwidth up to 1.544 Mbps** and **T3 links supporting bandwidth up to 43.7 Mbps**

- **E-carrier** : Used in Europe, E-carrier provides **E1 links support bandwidth up to 2.048 Mbps** and **E3 links supporting bandwidth up to 34.368 Mbps**

*Copper cable infrastructure has largely been replaced by optical fiber network*
### Leased Lines

![[Pasted image 20231213170629.png]]
## Circuit-Switched Options

Provided by **Public Service Telephone Network (PSTN) carriers** => local loop connecting the **CPE to the CEO is copper media** 
### Two traditional circuit-switched options

- **Public Service Telephone Network (PSTN)**

Dialup WAN access **uses PSTN as WAN connection** => traditional local loops **can transport binary computer data through the voice telephone network using voiceband modem** 

- **Integrated Services Digital Network (ISDN)**

Circuit-switching technology that **enables PSTN local loop to carry digital signals** => provide **higher capacity switched connections than dialup access** => **data rate from 45 Kbps to 2048 Mbps**

ISDN **declined greatly in popularity due to high-speed DSL** => ISDN considered **legacy technology** with most major providers stop this service

## Packet-Switched Options

- **Packet switching segments data into packets** => Circuit-switched **require dedicated circuit to be established** 
- Packet-switching => **allow many pairs of nodes to communicate over the same channel**

*2 traditional (legacy) packet-switched connectivity options

### Frame Relay

- **Simple Layer 2 non-broadcast multi-access (NBMA)** => interconnect enterprise LANs

=> **Single router interface** can be **used to connect to multiple sites using different PVCs**

=>  PVC **carry voice and data traffic between a source and destination (Data rates up to 4 Mbps and more for some providers) 

=> PVCs are **uniquely identified by a data-link connection identifier (DLCI)** => PVCs and DLCI **ensure bidirectional communication from one DTE device to another**

*Frame Relay networks have been largely replaced by faster Metro Ethernet and internet-based solutions*

### Asynchronous Transfer Mode (ATM)

- **Capable of transferring voice, video and data through private and public networks**

=> build on a **cell-based architecture rather than a frame-based architecture**
=> ATM cell always **fixed length of 53 bytes** => **5-bytes ATM header** and **48 bytes of ATM payload**

Fixed-length cells are *well-suited for carrying voice and video traffic

=> **ATM less efficient than the bigger frames and packets of Frame Relay** 
=> **Typical ATM line need 20% greater bandwidth than Frame Relay to carry the same volume of network layer data**

*ATM networks have been largely replaced by faster Metro Ethernet and internet-based solutions*

## Modern WANs

**Traditional WAN connectivity options have rapidly declined** => **too expensive, no longer available and limited bandwidth**

![[Pasted image 20231215131905.png]]

## Modern WAN Connectivity Options

![[Pasted image 20231215132146.png]]

- **Dedicated Broadband**

Late 1990s => telecommunication companies **build optical fiber networks with enough fiber to satisfy projected next generation need** => optical technologies **Wavelength Division Multiplexing WDM** => **increase transmitting ability of a single strand of optical fiber**

Many fiber-optic cable runs **are not in use** => **dark fiber** => **more expensive than any other WAN option** => **great flexibility, control, speed and security**

- **Packet-Switched** 

Two packet-switched WAN network options available

=> Metro Ethernet provide **fast bandwidth link** => responsible for **replacing many traditional WAN connectivity options**

=> Multi-protocol Label Switching (MPLS) **carry any protocol (IPv4 packets, IPv6 packets, Ethernet, DSL) as payload data** Enables different sites to **connect to the provider network regardless of its access technologies

- **Internet-based Broadband**

=> Commonly using **global internet infrastructure for WAN connectivity** => **combined with VPN** technologies to address **security concerns

## Ethernet WAN 

Ethernet was originally **developed as LAN access technology** => **not suitable as WAN** access technology due primarily to **limited distance provided by copper media**

=> **Newer Ethernet standards using fiber-optic cabled** => made Ethernet reasonable WAN access option 

=> **IEEE 1000BASE-LX standard supports fiber-optic cable lengths of 5 km**
=> **IEEE 1000BASE-ZX standard supports cable lengths up to 70 km**

Some provider **offer Ethernet WAN service using fiber-optic cabling** => many names including

- **Metropolitan Ethernet (Metro E)**
- **Ethernet over MPLS (EoMPLS)**
- **Virtual Private LAN Service (VPLS)

![[Pasted image 20231216140654.png]]

**Several benefits of Ethernet WAN**

- **Reduced expenses and administration :** Switched high-bandwidth Layer 2 network capable of managing data, voice and video all on the same infrastructure => increases bandwidth and eliminates expensive conversions to other WAN technologies => inexpensively connect numerous sites in the metropolitan area

- **Easy integration with existing networks :** Ethernet WAN connects easily existing Ethernet LAN => reducing installation costs and time

- **Enhanced business productivity** : take advantage of productivity-enhancing IP applications that are difficult to implement on TDM or Frame Relay networks => hosted IP communications, VoIP and streaming and broadcast video

*Ethernet WANs gained popularity and commonly used to replace traditional serial point-to-point, Frame Relay and ATM WAN links*

## MPLS

Multiprotocol Label Switching is **high-performance service provider WAN routing to interconnect clients** => **without regard to access method or payload** 

=> Variety of client access methods (Ethernet, DSL, Cable, Frame Relay)  => MPLS can encapsulate all types of protocols including IPv4 and IPv6 traffic

![[Pasted image 20231217004206.png]]

MPLS router can be => **customer edge (CE) router**, **provider edge (PE)** or **internal provider (P) router** 

MPLS router are **label switched routers (LSRs)** => they **attach labels to packets when traffic leaving CE** => **adds a short-fixed length in between the frame header and packet header** => MPLS P use label to determine **next hop of the packet** 

=> **Label removed by the egress PE** router **when packet leaves the MPLS network

MPLS provide also => **QoS support, traffic engineering, redundancy and VPNs**

## Internet-Based Connectivity Options

**Modern WAN connectivity do not end with Ethernet WAN and MPLS**

=> Host of internet-based wired and wireless options from which to choose

![[Pasted image 20231217005153.png]]

**Wired options**

=> Use **permanent cabling** (copper or fiber) to provide **consistent bandwidth** and **reduce error rates** and **latency**
=> like **Digital Subscriber Line (DSL)**, **cable connections** and **optical fiber networks

**Wireless options** 

=> **less expensive** to implement compared to other WAN connectivity options because **use radio waves instead of wired media to transmit data** 

=> can be **negatively affected** such as **distance from radio towers**, **interference from other sources**, **weather** and **number of users accessing the shared space** 

=> Wireless broadband include **3G/4G/5G or satellite internet services** 

## DSL Technology

Digital Subscriber Line (DSL) is **high-speed, always-on, connection technology that uses existing twisted-pair telephone lline to provide IP services to users** 

=> DSL is a **popular choice from home users and for enterprise IT departments to support teleworkers**

![[Pasted image 20231217010047.png]]

*entire frequency range supported by the copper wire pair

=> **POTS** area (**Plain Old Telephone System**) identifies the **frequency range used by voice-grade telephone service**
=> ADSL represents frequency space used by upstream and downstream DSL signals

Several xDSL varieties **offering different upload and download transmission rates** like **Asymmetric DSL (ADSL), Symmetric DSL (SDSL), ADSL and ADSL2+ provide higher downstream bandwidth to the user than upload bandwidth

SDSL **provide same capacity in both directions**

=> Transfer rates **dependent of the actual length of the local loop, the type and condition of the cabling

=> ADSL must be less than 5.46 km for **guaranteed signal quality**

## DSL Connections

- Connection **set up between DSL modem** and **DSL access multiplexer (DSLAM)**

![[Pasted image 20231217114001.png]]

- **DSL modem convert Ethernet signal to DSL signal** transmitted **to a DSL access multiplexer (DSLAM)** at the provider location

- **DSLAM located at the Central Office CO** of the provider and **concentrates connections from multiple DSL subscribers**

- **Advantage** => DSL is **not a shared medium** => **Each user has separate direct connection to the DSLAM**
## DSL and PPP

ISPs still use PP as the Layer 2 protocol for broadband DSL connections

- **PPP** use to **authenticate the subscriber**
- **PPP** assign a **public IPv4 address to the subscriber**
- **PPP** provides **link-quality management features**

*DSL modem has a DSL interface to connect to DSL network and Ethernet interface to connect client

### Host with PPPoE Client

- **Obtain a public IP address** from **PPPoE server at the provider site**
- PPPoE client **communicates with DSL modem using PPPoE** and modem **communicates with the ISP using PPP

![[Pasted image 20231218130806.png]]

Only one client can use the connection <=> no router to protect the inside network

### Router PPPoE Client

- **Configure router to be PPPoE client** => **obtains configuration from the provider** 
- Client communicate with router using **only Ethernet and unaware of the DSL connection** 

![[Pasted image 20231218130833.png]]

- **Multiple client can share the DSL connection**

## Cable Technology

- **High-speed always-on connection technology** => **uses coaxial cable from provider** => popular choice for home user and enterprise IT department to support remote workers

- **Modern cable systems** => **customers advanced telecommunications services** => high-speed internet access, digital cable television and residential telephone service

*DOCSIS (Data over Cable Service Interface Specification) => international standard for adding high-bandwidth data ton an existing cable system 

=> Cable operators deploy **hybrid fiber-coaxial (HFC) networks** => **high speed** => **carry radio frequency (RF) signals to the end user**

**HFC** => fiber optic and coaxial cable in different portions of the network

![[Pasted image 20231218131559.png]]

- Optical node : **perform optical (light pulses over fiber-optic cable) to RF signals conversion**

Fiber enable to travel long distances to **CMTS (Cable Modem Termination System)**

- Headend **contains databases needed to provide internet access
- CMTS **responsible for communicating with the cable modems**

**All local subscriber share the same cable bandwidth** => **if more users** => **bandwidth may drop**

## Optical Fiber

FTTx includes :

- **Fiber to the Home (FTTH)** : Reaches boundary of the residence => **Passive optical networks and PPP Ethernet Architecture** => **deliver TV, internet and phone services over FTTH** 

- **Fiber to the Building (FTTB)** : Reaches boundary of the building => to the individual living space via alternative like curb or pole technologies

- **Fiber to the Node/Neighborhood (FTTN)** : Reaches an optical node => convert optical signal to format acceptable for twisted pair or coaxial cable to the premise

## Wireless Internet-Based Broadband

Uses **unlicensed radio spectrum** to **send** and **retrieve data** => anyone who **has a wireless technology**

### Municipal Wi-Fi

- High-speed internet access **for free
- **Allowing police, fire department and other to do their jobs remotely
- **Need of wireless modem to connect** => stronger radio and directional antenna than conventional

### Cellular

- Radio waves to communicate **through a nearby mobile phone tower
- **3G/4G/5G Wireless** => **4G up to 450 Mbps download and 100 Mbps upload** => **5G support 100 Mbps to 10 Gbps and beyond**
- **Long-Term Evolution (LTE)** => Newer and faster technology of 4G

### Satellite Internet

- **Used when no cable and DSL available** => remote locations
- **Need of satellite dish** => **two modem (uplink and downlink)** => **coaxial cable between dish and modem
- Router **connect to the satellite dish** => which **pointed to a service provider satellite** => in geosynchronous orbit in space at 35,786 klm
- **Required clear view toward equator** => where **most orbiting satellites are stationed** 
- **Trees and Heavy rains affect reception of the signals**
- Upload speed 1/10 of the download speed => download speeds range from **5 to 15 Mbps**

### WiMAX

- Worldwide Interoperability for Microwave Access => IEEE standard 802.16
- Operates similar way to Wi-Fi but a **higher speed, greater distances and number of users** 
- **Use network of WiMAX towers like cell phone towers** 
- **User subscribe to an ISP with WiMAX tower within 30 miles of their location**

*Largely replaced by LTE for mobile access and cable or DSL fixed access*

### VPN Technology

- **Encrypted connection** between **private networks over public network

Instead of Layer 2 connection such as leased line => VPN uses **virtual connections called VPN tunnels

Routed => through internet from the private network of the company to the remote site

**Benefits**

- **Cost saving** : eliminates need of dedicated WAN links and modem banks

- **Security** : Highest level of security with advanced encryption and authentication protocols that protect data from unauthorized access

- **Scalability** : Use internet infrastructure within ISPs and devices => easy to add new users
 
- **Compatibility with broadband technology** : such as DSL and cable 

*VPN implemented as the following*

- **Site-to-site VPN** : config on router
- **Remote Access** : HTTPS browser

## ISP Connectivity Options

- **Single-Homed** : client connect to ISP using one link

![[Pasted image 20231218222134.png]]

- **Dual-Homed** : Using Two links => **redundancy and load balancing 

![[Pasted image 20231218222205.png]]

- **Multihomed** : Connect to two different ISPs => **increased redundancy, load-balancing** => **can be expensive**

![[Pasted image 20231218222226.png]]

- **Dual-multihomed** : **Most resilient** => **multiple ISP with redundant links** => **most redundancy possible** => **most expensive**

![[Pasted image 20231219175707.png]]

## Broadband Solution Comparison

- **Cable** : Bandwidth **shared with many users** => data rates **can be slow in area with over-subscription

- **DSL** : **Limited bandwidth** => **distance sensitive** => **Upload lower than download rate

- **Fiber-to-the-Home** : Requires **installation directly to the home**

- **Cellular/Mobile** : Bandwidth **relatively limited**

- **Municipal Wi-Fi** : Viable option **when deployed**

- **Satellite** : **expensive** and **limited capacity per subscriber** => **when no other option available

![[Pasted image 20231219181514.png]]

*Coaxial Splitter splits digital data and video signals to send data signal to the cable modem and video signal to TV*

*DSL Modem convert data signals to Ethernet signals*
# ==VIII. VPN and IPsec Concepts==

## Virtual Private Networks

- **End-to-end private network connections**
- Carries information within **private network** transported over public network => traffic encrypted

![[Pasted image 20231219203606.png]]

=> **First type of VPNs were IP tunnels** that dit **no include encryption or authentication of the data** => GRE (Generic Routing Encapsulation) developed **by Cisco** => to create a **virtual point-to-point link** to encapsulate IPv4 and IPv6 traffic

## VPN Benefits

Modern VPNs support **encryptions features** such as **Internet Protocol Security (IPsec)** and **Secure Socket Layer (SSL) VPN**

![[Pasted image 20231220082254.png]]

## Site-to-Site VPN

- When **VPN gateways** are **preconfigured  with information to establish secure tunnel** => VPN t**raffic only encrypted between these devices**

- Internal hosts **don't know that VPN is used

![[Pasted image 20231220082507.png]]

## Remote-Access VPN

- Establish **secure connection between a client and VPN terminating device**
- **SSL VPN used to check banking information online**

![[Pasted image 20231220082713.png]]
## Enterprise and Service Provider VPNs

- **Enterprise VPNs** : Enterprise-managed VPN for securing enterprise traffic across internet
- **Service Provider VPNs** : Created and managed over provider network

![[Pasted image 20231220083104.png]]

## Remote-Access VPNs

- **Enabled dynamically by user** => **using IPsec or SSL** 

![[Pasted image 20231220083632.png]]

## SSL VPNs

- When client **negotiates SSL VPN with VPN gateway** => **it connect using Transport Layer Security (TLS)** 
- **TLS newer version of SSL** => expressed as **SSL/TLS** 

![[Pasted image 20231220083924.png]]

**IPsec and SSL VPNs** are **complementary** => **solve different problems** depending of **needs of its telecommuters**

## Site-to-Site IPsec VPNs

- **Used to connect networks across untrusted network**
- Host **send and receive normal unencrypted TCP/IP traffic through a VPN terminating device**
- **VPN terminating is** called **VPN gateway**

*Such as Cisco Adaptive Security Appliance (ASA)* => standalone firewall with VPN concentrator, intrusion prevention

![[Pasted image 20231220084431.png]]

- **VPN gateway encapsulated and encrypt outbound traffic**
- **VPN gateway strips the headers, decrypts the content, relays the packet to the target host**
## GRE over IPsec

- **Generic Routing Encapsulation (GRE)** is a **non-secure site-to-site VPN tunneling protocol** => **encapsulate various network layer protocols** => **support multicast and broadcast traffic** => which be necessary of organization requires **routing protocol to operate over VPN**

- **GRE no encryption** / **secure VPN tunnel**

- **Standard IPsec VPN (non-GRE)** can create tunnels for unicast traffic => **routing protocols will not exchange routing information over IPsec VPN**

![[Pasted image 20231220090805.png]]

- To solve this problem => **Encapsulate routing protocol traffic using GRE packet** => **then encapsulate GRE packet into IPsec packet to forward it securely to destination VPN gateway**

*Term used to describe encapsulation of GRE over IPsec tunnel are passenger protocol, carrier protocol, transport protocol*

**Exchange OSPF routing information over IPsec VPN**

- **IPsec not support multicast traffic** 
- **GRE over IPsec used to support routing protocol traffic over IPsec**
- **OSPF packet encapsulated by GRE and subsequently encapsulated in IPsec VPN tunnel**

![[Pasted image 20231220090941.png]]

*OSPF Hello packet sent using GRE over IPsec*

![[Pasted image 20231220091142.png]]

## Dynamic Multipoint VPNs

- **Site-to-site VPNs** and **GRE over IPsec** adequate **when few sites to securely interconnect

=> **not sufficient when enterprise add many more sites** => would **require static configuration to all other sites**

- **Dynamic Multipoint VPN (DMVPN)** is **Cisco software solution** for **building multiple VPN easy, dynamic and scalable**

=> **Relies on IPsec for secure transports
## DMVPN Hub-to-Spoke Tunnels

- **Simplifies VPN tunnel configuration** and **provides flexible option to connect a central site with branch sites**

![[Pasted image 20231220091645.png]]

- **Each site configured with Multipoint Generic Routing Encapsulation (mGRE)**

- mGRE tunnel interface **allows single GRE interface to dynamically support multiple IPsec tunnels**

- **Spoke site can obtain information about other spoke** => **create virtual spoke-to-spoke tunnels**
## DMVPN Hub-to-Spoke and Spoke-to-Spoke Tunnels

![[Pasted image 20231220092119.png]]
## IPsec Virtual Tunnel Interface

- **Simplifies configuration process** required to **support multiple sites** and **remote access**

- **IPsec VTI applied to virtual interface** =>   **instead of static mapping the IPsec sessions to a physical interface**

- **IPsec VTI capable of sending and receiving IP unicast and multicast encrypted traffic** 

=> Routing protocols automatically supported **without GRE tunnel

![[Pasted image 20231220093451.png]]

## Service Provider MPLS VPNs

- **Traditional service provider WAN as leased lines, Frame Relay and ATM** =>  **insecure

- **Today, MPLS in core network of service provider** => traffic **through MPLS backbone using label** => **Service provider customers cannot see each other's traffic**

- **Layer 3 MPLS VPN** : Service provider **participates customer routing by establishing peering between customer's routers and provider's routers** => customer routes **received by provider's router** are **redistributed through MPLS network to customer's remote locations**

- **Layer 2 MPLS VPN** : **Not involved in customer routing** => **deploys Virtual Private LAN Service (VPLS)** to **emulate Ethernet multiaccess LAN segment over MPLS network** => **no routing involved** => customer's routers **belong to the same multiaccess network**

![[Pasted image 20231220093813.png]]
## IPsec Technologies

- **IPsec** => **IETF standard (RFC 2401-2412)** defines **how VPN can be secured across IP networks**

- **Protects and authenticates IP packets between source and destination** (From Layer 4 through Layer 7)

**Confidentiality :** encryption algorithms

**Integrity :** hashing algorithms to ensure have not been altered

**Origin Authentication** : Uses of **Internet Key Exchange (IKE)** protocol to **authenticate source and destination** such as pre-shared keys (passwords), digital certificates or RSA certificates

**Diffie-Hellman** : Key exchange using various groups of the DH algorithm

*IPsec framework can be filled with any of the choices that are available*

![[Pasted image 20231220102250.png]]**

![[Pasted image 20231220102312.png]]
### IPsec Security Association Examples

![[Pasted image 20231220102527.png]]

## IPsec Protocol Encapsulation 

- **IPsec encapsulates packets** => **Authentication Header (AH)** or **Encapsulation Security Protocol (ESP)** 

![[Pasted image 20231220104855.png]]

- **AH** => **confidentiality no required or permitted** => provides **data authentication and integrity** => **text unencrypted 

- **ESP** => **confidentiality and authentication** => **encryption on IP packet

- **ESP** => provides **authentication for the inner IP packet and ESP header** => **Authentication provide data origin authentication and data integrity** => **encryption and authentication are optional in ESP** => At minimum, **one of them must be selected**
## Confidentiality

- Degree of confidentiality **depends on encryption algorithm** and **length of key used**

- **The shorter the key**, the **easier it is to break**

- **64-bit key** take approximately **one year to break with sophisticated computer** => **128-bit key** take roughly **10 quintillion 10^19 years to decrypt**


![[Pasted image 20231220105727.png]]

- **All symmetric key cryptosystems**

![[Pasted image 20231220105816.png]]

- **DES** should **be avoided
- **3DES** => **variant of 56-bits DES** => **no secure anymore
- **AES** => **most recommended symmetric encryption algorithm** => **stronger security DES** and **more efficient than 3DES** => three different key lengths **128 bits, 192 bits and 256 bits**
- **SEAL** => stream cipher => encrypt data continuously rather than **encryption block of data** => SEAL use **160-bit key** => **very secure**
## Integrity

- **Data integrity** => data **received exactly same data that was sent**
- Data could **be intercepted and modified**

![[Pasted image 20231220111218.png]]

- **HMAC** (Hashed Message Authentication Code) is a data integrity algorithm that **guarantees integrity of the message using hash value** 

- The two most common **HMAC algorithm** : SHA and MD5

Cisco recommend *SHA-1* as legacy and *SHA-256* for integrity

![[Pasted image 20231220111441.png]]

- **Message-Digest 5 (MD5)** uses **128 bit shared-secret key** => **variable-length message** and **128-bit shared secret key combined** and **run with HMAC-MD5 hash algorithm** => The output is **128-bit hash** => no longer secure, should be avoided

- **Secure Hash Algorithm** (SHA) **uses** a 160-bit secret key => **variable-length message** and **160-bit shared secret keys combined** => **run HMAC-SHA-1** algorithm => The output is **160-bit hash** => **SHA-256 or higher are considered to be secure** 
## Authentication

- **VPN tunnel** must be **authenticated before communication path** 

![[Pasted image 20231220111837.png]]

- **Pre-shared secret key (PSK)** =< **entered into each peer manually** => combined with **other information to form authentication key** => **easy to configure manually** => **do not scale well** => **IPsec peer must be configure with configured PSK of every other peer with which it communicates**

- **Rivest, Shamir and Adleman (RSA)** authentication uses **digital certificates to authenticate peers** => local device **derives a hash and encrypt it with its private key** => **encrypted hash attached to message and forwarded** => the remote end **decrypted** the **encrypted hash** with **the public key** => if **decrypted hash matches the recomputed hash** => signature **is genuine**
# PSK Authentication 

**PSK (Pre-Shared Key) Authentication:**

- This method involves a **shared secret key** known to both the local and remote devices.
- During **authentication**, the local device combines its **authentication key and identity information**, which is processed through a hash algorithm to **create a hash (Hash_L).

![[Pasted image 20231220135430.png]]

- This hash is **sent to the remote device.
- The remote device **independently generates a hash using the same method and compares it with the received hash.
- If the **hashes match, authentication is successful.
- The process is then repeated in the **opposite direction for two-way authentication.**
## RSA Authentication

**RSA (Rivest-Shamir-Adleman) Authentication:**

- RSA is a **public-key encryption technique.**
- The local device combines its **authentication key and identity information to create a hash (Hash_L).
- This hash is then **encrypted with the local device's private key to create a digital signature.
- The **digital signature, along with a digital certificate (which contains the public key), is sent to the remote device.

![[Pasted image 20231220143108.png]]

- The remote device uses the **public key to decrypt the digital signature and obtain Hash_L.
- It then independently **generates Hash_L using stored information.
- **If the calculated Hash_L matches the decrypted Hash_L, the authentication is successful.
- The process is mirrored **from the remote device to the local device for complete authentication.

## Secure Key Exchange with Diffie-Hellman

- **Encryption algorithm require symmetric, shared secret key to perform encryption and decryption**

=> easiest key exchange method to use **public key exchange method** => **Diffie-Hellman (DH)

![[Pasted image 20231220144115.png]]

- Establish a shared secret key

- **DH groups 1, 2 and 5** should no be used with **key size of 768 bits, 1024 bits and 1536 bits**
- **DH groups 14,15,16** use larger **key sized with 2048 bits, 3072 bits and 4096 bits** recommended for use until 2030
- **DH groups 19, 20, 21 and 24** with respective **key sizes of 256 bits, 384 bits, 521 bits and 2048 bits** support Elliptical Curve Cryptography (ECC) => reduce time needed to generate keys => **DH 24 preferred next generation encryption

*Use group 21 or 24*
# ==IX. QoS Concepts==

- **Congestion when multiple communication lines aggregate onto a single device**

- **Congestion occurs when large data packets prevent smaller packets from being transmitted in a timely manner**

- **Volume of traffic greater than what can be transported across the network** => **devices queue (hold) packets in the memory until resources become available to transmit them**

![[Pasted image 20231220154459.png]]

- **Queuing packet** => **cause delay** => **new packets cannot be transmitted until previous packets have been transmit** 

=> if number of packet in queued continues to **increase** => **memory within device fills up and packets dropped** => we can **classify data** into **multiple queues**

## Bandwidth, Congestion, Delay and Jitter

- **Network bandwidth** => **number of bits transmitted in a single second

- Congestion when **more traffic than can't be handle**

![[Pasted image 20231221214831.png]]

- **Delay** => **time for the packet from the source to the destination** 

- Fixed delay => **Amount of time** a **specific process takes** => like **how long it takes to place a bit on the transmission media**

- Variable delay => **unspecified amount of time** => **affected by factors such as how much traffic is processed** 

![[Pasted image 20231221215225.png]]

**Jitter** => **variation in the delay of received packets** 

Network congestion **improper queuing or configuration errors, the delay between each packet can vary instead of remaining constant** 

=> **Delay and jitter need to be controlled and minimized to support real-time and interactive traffic**

## Packet Loss

- **Without QoS** => **packets processed in order they are received** => **can cause drop of packets when congestion**

- **Time-sensitive packet could be drop as well as data that is not time-sensitive**

- **When Real-Time Protocol digital audio stream for Voice over IP (VoIP)** => **it must be compensate for the jitter encountered** => with **playout delay buffer**

![[Pasted image 20231223083926.png]]

- When **jitter large** => **received out of range** => **out-of-range packet discarded and dropouts**  

![[Pasted image 20231223084059.png]]

- **DSP (Digital Signal Processor)** => for losses **as small as one packet** => **Is not a problem** => **If jitter exceed the DSP can do** => **audio problems are heard** 

- **In properly designed network** => **packet loss should be near zero** 

=> With QoS mechanisms

## Network Traffic Trends

- in **early 2000s** => predominant types of IP traffic were **voice and data

```
Voice traffic => predictable bandwidth need and packet arrival times

Data traffic => not real-time and unpredictable bandwidth need
```

=> Data traffic can **temporarily burst the entire bandwidth of a link (with downloaded)

=> **In 2022** => **video represent 82% of all internet traffic
## Voice 

- Voice traffic => **predictable and smooth** => sensitive **to delays and dropped packets** => **voice never re-transmit** (no sense)

- **Cisco have RTP port range from 16384 to 32767 to prioritize voice traffic** 

![[Pasted image 20231223085956.png]]
## Video

- Without QoS and significant amount of extra bandwidth capacity, video quality **degrades**

=> picture are **blurry, jagged, in slow motion** => **audio become unsynchronized with video**

- Video => **unpredictable, inconsistent and bursty compared to voice traffic**

- Voice => **less resilient to loss** and **higher volume of data per packet** => **20ms and predictable 200 bytes each**

![[Pasted image 20231223090943.png]]

- Video => **number and size of video packet varies every 33 ms** => **based on the content of the video**

![[Pasted image 20231223091256.png]]

- UDP port such as **554** => **Real-Time Streaming Protocol (RSTP)** => **priority over less delay-sensitive, network traffic

![[Pasted image 20231223091657.png]]

## Data 

- Application use either **TCP or UDP** => **TCP performs error recovery** => use it with **data application with no tolerance for data loss** => data can **be resent

- **When change in topology** => **network control traffic may burst for a few seconds** => **capacity of today's networks easily handle the increase in network control traffic** 

![[Pasted image 20231223092414.png]]

- **Quality of Experience or QoE** (quality of the user experience) => should be consider 

	- Does the data come from an interactive application ?
	- Is the data mission critical ?


![[Pasted image 20231223092639.png]]
## Queing Overview

*Queuing Algorithm

- **First-In, First-Out (FIFO)**
- **Weighted Fair Queuing (WFQ)**
- **Class-Based Weighted Fair Queuing (CBWFQ)**
- **Low Latency Queuing (LLQ)**

## First In First Out

- **Simplest form => first-served queuing, buffers and forwards in order of their arrival**

- **All interface except serial interfaces** => **use FIFO by default**
- **E1 (2.048 Mbps)** => **use WFQ by default** 

![[Pasted image 20231223094619.png]]

## Weighted Fair Queuing (WFQ)

=> **Applies priority or weights** => **identified traffic** and **classified into conversations or flows**

- **WFQ** determines **how much bandwidth each flow is allowed relative to other flows** 

=> **Schedule traffic to the front of a queue to reduce response time** => **fairly shares the remaining bandwidth among high-bandwidth flows**


![[Pasted image 20231223095005.png]]

=> **WFQ allow you to give low-volume**, **interactive traffic** such **Telnet sessions** and **voice** priority over **high-volume traffic** such **as FTP sessions**

- **When multiple file transfers flow simultaneously** => **given comparable bandwidth**

WFQ => **classifies traffic based on** => **source and destination IP addresses, MAC addresses, port numbers, protocol and Type of Service (ToS) value** => ToS value in the **IP header used to classify traffic

Low-bandwidth traffic **receive preferential service which allows entire offered load to be sent in timely fashion**

High-volume traffic flow **share remaining capacity proportionally among themselves**

#### Limitations

=> **not supported with tunneling and encryption** => features **modify packet content information required by WFQ for classification**

=> **WFQ automatically adapts to changing network traffic conditions** => not offer **the degree of precise control over bandwidth allocation that CBWFQ offers**

## Class-Based Weighted Fair Queuing (CBWFQ)

- **Extends the standard WFQ functionality** => **Support user-defined traffic classes** 

- **based on match criteria** => **protocols**, **access control lists (ACLs)** and **input interfaces**

- **FIFO queue for each class**

![[Pasted image 20231223102826.png]]

- **Class defined with match criteria*** => we can **assign bandwidth**, **weight**, **maximum packet limit**

- **Packets belonging to a class** =>**subject only to the bandwidth** and **queue limits** that **characterize the class**

- **If queue limit reached** => **adding more packets to the class causes tail drop or packet drop** 

	- **tail drop** => router **discards any packet** that **has completely used up its packet-holding resources**

## Low Latency Queuing (LLQ)

- **Brings strict priority queuing (PQ) to CBWFQ** => **allows delay-sensitive packets such as voice to be sent before packets in others queues** => **reducing jitter in voice conversations** 

=> Without LLQ => **CBWFQ provides WFQ** based on defined classes with **no strict priority queue available for real-time traffic** 

=> **LLQ** => **allows delay-sensitive packets** such as **voice to be sent first** (before packets in other queues)

![[Pasted image 20231223105147.png]]

=> **giving delay-sensitive packets preferential treatment** over **other traffic** 

=> **Cisco** recommends only voice traffic **be directed to the priority queue**

## Selecting an Appropriate QoS Policy Model

- **Best-effort model**
- **Integrated services** (IntServ)
- **Differentiated services** (DiffServ)

![[Pasted image 20231223151511.png]]

## Best Effort

- **Treats all network packets in the same way**

![[Pasted image 20231223151718.png]]

## Integrated Services

- **IntServ architecture model (RFC 1633, 2211 and 2212)** => **developed in 1994** => **meet the need of real-time applications** => such as remote video, multimedia conferencing, data visualization applications and virtual reality

- Delivers **end-to-end QoS** => **real-time applications require** => provide **QoS to individual flows or streams** => called microflows

=> **Similar concept known as "hard QoS"** => guarantees **traffic characteristics** such a bandwidth, delay and packet-loss rates **from end to end**

### Simple IntServ Example

![[Pasted image 20231223152457.png]]

- **Connection-oriented approach inherited from telephony network design** 

=> Each individual communication must **explicitly specify its traffics descriptor and requested resources to the network** 

- **Edge router** => **performs admission control** => **ensure available resources are sufficient in the network**

IntServ model **requests** specific kind of service **from the network sending data** => **informs network of its traffic profile and requests particular kind of service** => that **can encompass its bandwidth and delay requirements** 

IntServ uses **Resource Reservation Protocol (RSVP)** to signal the **QoS needs** of an application's traffic along devices int the **end-to-end path through the network** => 

if network devices along the path can **reserve the necessary bandwidth** => **the originating application can begin transmitting** => if reservation fails then originating application does **not send data

- **Edge router** performs admission control based **on information** from the application and **available network resources** 

=> Network commits the meeting the QoS requirements of the application as long as **traffic remains within the profile specifications** 


![[Pasted image 20231223154815.png]]
## Differentiated Services

- **Provide low-latency to critical network traffic** as voice or video
- **Provide simple best-effort traffic guarantees to non-critical services** as web traffic or file transfers

=> DiffServ overcomes **limitations of both the best-effort and IntServ models** => described in **RFCs 2473, 2597, 2598, 3246 and 4594** => almost guaranteed QoS while **still being cost-effective and scalable

=> Not an **end-to-end QoS strategy** => is a more **scalable approach to implementing QoS** => **Unlike IntServ and hard QoS which end-hosts signal their QoS needs to the network** => DiffServ not use **signaling** 

- **Uses of 'soft QoS' approach** => network element are **set up to service multiple classes of traffic each varying QoS requirements**

### Simple DiffServ Example

![[Pasted image 20231223155908.png]]

- Router classifies the **flows into aggregates (classes) and provides appropriate QoS policy for the classes**

=> DiffServ **enforces and applies QoS mechanism on a hop-by-hop basis** => provide **flexibility and scalability** 

=> Classes based **on business requirements** => **with different level of service** => **each network devices identifies the packet class and services the packet according to that class** 

=> Voice traffic from IP phones **usually given preferential treatment over all other application traffic** => email is **best-effort** and **non-business traffic either be given very poor service or blocked entirely**

![[Pasted image 20231223160502.png]]

## Avoiding Packet Loss

- Packet loss => **result of congestion on an interface** 
- Most applications use **TCP** => **experience slowdown** =>  because **TCP automatically adjusts to network congestion** 
- Dropped **TCP segments cause TCP sessions to reduce their windows size** 

=> but some application **don't use TCP and can't handle drops** 

*To prevent drops

- **Increase link capacity to ease or prevent congestion**
- **Guarantee enough bandwidth and provide prioritized forwarding to drop-sensitive application** => **WFQ, CBWFQ and LLQ** can do that
- **Drop lower-priority packet** => before congestion occurs => **Cisco IOS QoS provides queuing mechanisms** such weighted random early detection (WRED) 

## QoS Tools

![[Pasted image 20231224084511.png]]
![[Pasted image 20231224084722.png]]

- **Ingress packets (gray squares)** => **classified and IP header marked (colored squares)** => packets **are allocated resources based on policies** => **Packets are queued** and **forwarded** out the **egress interface based on QoS shaping and policing policy**

## Classification and Marking

- **Mark types of packets** => class of traffic **which packets or frames belong

- **Only after marked traffic can be applied to it**

=> Methods of classifying **traffic flows at Layer 2 and 3 include using interfaces, ACLs and class maps** => can be **classified at Layers 4 to 7 using Network Based Application Recognition (NBAR)

=> Marking **means adding value to the packet header** => **Device receiving** the packet => **look the market field** to see if it matches a defined policy => **Marking should be done as close to source device** 

![[Pasted image 20231225105649.png]]
## Marking at Layer 2

- **802.1Q** => IEEE Standard that **supports VLAN tagging at Layer 2** on **Ethernet networks** => When implemented => **two fields added to the Ethernet Frame

![[Pasted image 20231225105923.png]]

- **802.1Q standard** includes **QoS prioritization scheme known as IEEE 802.1p** => 

- 802.1p => uses **first three bits in Tag Control Information (TCI) field** => know as **Priority (PRI) field** => **3-bit field identifies Class of Service (CoS)**

![[Pasted image 20231225110318.png]]
## Marking at Layer 3

- **IPv4 and IPv6 specify an 8-bit field** in their packet headers to **mark packets

=> **Type of Service (ToS) field for IPv4**
=> **Traffic Class field for IPv6**

![[Pasted image 20231225110521.png]]

## Type of Service and Traffic Class Field

=> **RFC 791** => Original IP standard **specified the IP Precedence (IPP)** field to be used for **QoS markings** => **3 bits used out of 8 bits reserved => did not provide enough granularity to implement QoS**

=> **RFC 2474** => redefines **ToS field** => **6 bit allocated for QoS** => **Differentiated Services Code Point (DSCP) field** => maximum of **64 possibles classes of service** 

Remaining **two IP Explicit Congestion Notification (ECN) bits used by ECN-aware routers to mark packets instead of dropping them** => ECN marking **informs downstream routers** that **there is congestion in the packet flow

![[Pasted image 20231225111312.png]]
## DSCP Values

*3 Categories :

- **Best-Effort (BE)** : Default for all IP packets => DSCP value is 0 => normal routing => when router experiences congestion => packet will be dropped => No QoS plan implemented

- **Expedited Forwarding (EF) :** RFC 3246 => EF as **DSCP decimal value 46** (101110) => the **first 3 bits** (101) map directly to the Layer 2 CoS value 5 used for **voice traffic**. Cisco **recommends** at **Layer 4** EF only use to mark voice packet

- **Assured Forwarding (AF) :** RFC 2597 => AF **use 5 most significant DSCP bits** indicate queues and drop preference 

![[Pasted image 20231226101257.png]]

*AF41 means CLASS 4 and Low Drop for 34 decimal value

*For the class :

- Classe 1 : 001
- Classe 2 : 010
- Classe 3 : 011
- Classe 4 : 100

*To count the decimal value*

In the example : 

0  1  1  1  0  0
2  4  8  16 32

The seventh bit are always at 0 
## Class Selector Bits

**3 bits of CoS field and IPP field to maintain compatibility with 802.1p and RFC 791** 

![[Pasted image 20231226103112.png]]

- **Map IPP values to Class Selectors**

![[Pasted image 20231226103300.png]]

## Trust Boundaries

- **Traffic should be classified and marked as close to its source**

1. Trusted endpoints can **mark application traffic** => to the **appropriate Layer 2 CoS and/or Layer 3 DSCP values** => like IP phones, wireless access points, videoconferencing gateways and systems, IP conferencing stations and more

2. Layer 2 switch can mark 

3. Layer 3 switches, routers can mark

![[Pasted image 20231226104505.png]]
## Congestion Avoidance

- **Congestion management** : Includes queuing and scheduling methods => **excess traffic is buffered or queued wait to be sent out an egress interface** 

- **Congestion avoidance** : Monitor network traffic to **anticipate and avoid congestion before congestion occurs** => can monitor the average depth of the queue => 
	- If queue below the minimum threshold => no drop
	- If queue fills up to maximum threshold => small percentage of packets drop
	- If maximum threshold is passed => all packets are dropped

![[Pasted image 20231226105134.png]]

Congestion avoidance techniques => **provide preferential treatment for which packets will get dropped

- Cisco IOS QoS include **weighted random early detection (WRED)** => providing **buffer management and allowing TCP traffic to decrease or throttle back** before buffers are exhausted => **increase application performance

- **Congestion avoidance** for **User Datagram Protocol (UDP)-based traffic** => such as voice traffic => **queuing and compression techniques to reduce and prevent UDP packet loss**

## Shaping and Policing

- **Traffic shaping retain excess packets in a queue and schedules excess for later transmission over increment of time** => smoothed packet output rate

![[Pasted image 20231226105843.png]]

- **Have sufficient memory when enabling shaping** => scheduling function for later transmission packet => to organize the shaping queue into different queues => **CBWFQ and LLQ** 

- In contrast => **policing applied to inbound traffic on interface** => **when traffic rates reaches configured maximum rate** => excess traffic **is dropped** (or remarked)

=> Use by service providers to **enforce contracted customer information rate (CIR) 

![[Pasted image 20231226110446.png]]

## QoS Policy Guidelines

**Consider full path from source to destination** 

=> To ensure best experience for users

- **Enable queuing at every device in the path between source and destination**
- **Classify and mark traffic as close the source as possible**
- **Shape and police traffic flows as close to their sources as possible** 

# ==X. Network Management==

## CDP Overwiew

- **How to use Cisco Discovery Protocol (CDP) to create a map of your network**

=> Cisco proprietary Layer 2 protocol => **used to gather information about Cisco devices which share the same data link** => CDP **media and protocol independent** and runs on **all Cisco devices** => such as routers, switches and access servers

![[Pasted image 20231226113023.png]]

- Information about **the type of device that is discovered, the name of the devices and number and type of the interfaces** 

## Configure and Verify CDP

For Cisco devices => **CDP enabled by default** => **it can be disable** for security reasons (to protect from attacker) 

- Display information about CDP => **show cdp**

```
Router# show cdp 
Global CDP information: 
		Sending CDP packets every 60 seconds 
		Sending a holdtime value of 180 seconds 
		Sending CDPv2 advertisements is enabled
```

- Enable CDP globally for all supported interface on device => **cdp run** 
- Disabled CDP on all interfaces on device **no cdp run

```
Router(config)# no cdp run
Router(config)# exit
Router# show cdp
CDP is not enabled
Router# configure terminal
Router(config)# cdp run
```

- Disable CDP on specific interface => **no cdp enable**
- Enable CDP on specific interface => **cdp enable** 

```
Switch(config)# interface gigabitethernet 0/0/1
Switch(config-if)# cdp enable
```

- Display list of neighbors => **show cdp neighbors** 

![[Pasted image 20231226114749.png]]

- Display interfaces CDP-enabled on device => **show cdp interface** 

![[Pasted image 20231226114840.png]]

## Discover Devices by Using CDP

![[Pasted image 20231226115451.png]]

- **Show cdp neighbors** => can be used to determine network layout

![[Pasted image 20231226115220.png]]

- **Device identifiers** : host name of the neighbor device (S1)
- **Port identifier** : name of the local and remote port (G0/0/1 and F0/5)
- **Capabilities list** : Device is a switch (S for switch and I for IGMP)
- **Platform** : Hardware platform of the device (WS-C3560 for Cisco 3560 switch)

*Cisco device S1 connected to G0/0/1 interface on R1, S1 through its F5/0

![[Pasted image 20231226122459.png]]

- **show cdp neighbors detail**

![[Pasted image 20231226122656.png]]

- **On R1 we ssh on S1 to look neighbors** => **show cdp neighbors**

![[Pasted image 20231226122956.png]]

- We can **update the schema

![[Pasted image 20231226123029.png]]

- Again **we can access to S2 with ssh to show the neighbors detail

![[Pasted image 20231226123204.png]]

Here we have only S1 on neighbors => **there are no more device to discover in the topology

## LLDP Overview

- Link Layer Discovery Protocol (LLDP) does same thing as CDP => **vendor-neutral neighbor protocol** 

![[Pasted image 20231226125924.png]]
## Configure and Verify LLDP

- Enable lldp => **lldp run**
- Disable sending of LLDP messages on an interface => **no lldp transmit** 
- Disabled receiving of LLDP messages on the interface => **no lldp receive**

![[Pasted image 20231226130031.png]]

## Discover Devices by Using LLDP

![[Pasted image 20231226130228.png]]

- Show device neighbors => **show lldp neighbors** 

![[Pasted image 20231226130241.png]]
![[Pasted image 20231226130323.png]]

- Show more detail => **show lldp neighbors detail**

![[Pasted image 20231226130406.png]]

## NTP Time and Calendar Services

- **Software clock on a router starts when system boot**

- **Important to synchronize the time across all devices on the network** =>**managing, securing, troubleshooting and planning networks** => without impossible to determine the order of the events and cause of the event

*2 Methods 

- **Manually configure** 

![[Pasted image 20231226132042.png]]

- **Configure Network Time Protocol (NTP)**

A better solution => **synchronize their time with NTP server** => it can be set up to synchronize to a **private master clock** or **synchronize with publicly available NTP server on internet**

=> NTP use **UDP port 123** and **documented in RFC 1305**

## NTP Operation

=> **Hierarchical system of time sources**  => Each level is **a stratum** => defined by **number of hop counts from the authoritative source**  

![[Pasted image 20231226152950.png]]

**Stratum 0**

- **NTP network get time from authoritative time sources** => such as **GPS clocks or atomic clock** are the most accurate authoritative time sources

- **non-network high-precision timekeeping devices** assumed to be accurate => represented by clock icon

**Stratum 1**

- **Directly connected to the authoritative time sources** => **primary network time standard to stratum 2 devices using NTP**

**Stratum 2 and Lower** 

- Connected to stratum 1 devices => such as NTP client => synchronize their time **by using NTP packet from stratum 1 servers** => they can also act as servers for stratum 3 devices

- **Smaller stratum number indicate the server is closer to the authorized time source than larger stratum number** => **Max hop count is 15** => **Stratum 16 is the lowest stratum level** => device is unsynchronized
## Configure and Verify NTP

![[Pasted image 20231226153738.png]]

- Show current time on the software clock => **show clock** => with detail **on how it's configured** => **show clock detail** 

![[Pasted image 20231226153916.png]]

- Specify NTP ip address => **ntp server 209.165.200.225**

![[Pasted image 20231226154002.png]]

- Verify that R1 is synchronized with NTP server => **show ntp associations** => Here we can see it's synchronized with GPS clock => **st is stratum

![[Pasted image 20231226154126.png]]

- Clock on **S1 is configured to synchronize to R1 with ntp server
- **NTP server is stratum 1**         //       **R1 stratum 2**       //        **S1 stratum 3

![[Pasted image 20231226155222.png]]
![[Pasted image 20231226155343.png]]
## Introduction to SNMP

- Simple Network Management Protocol (SNMP) => **allow administrator to manage nodes such as servers, workstations, routers, switches and security appliances** on IP network 

*Consist of three elements 

- SNMP manager
- SNMP agents (managed node)
- Management Information Base (MIB)

=> SNMP manager **runs SNMP management software** => **collect information from an SNMP agent** => using "**get**" action and **change configurations** on an agent by using "**set**" action 

= SNMP agent can forward information directly to a **network manager** by **using traps

![[Pasted image 20231226191601.png]]

- **SNMP agent and MIB reside on SNMP client devices** => **MIBs store data** and **available to authenticated remote users**  => **SNMP agent responsible for providing access to the local MIB**

- **SNMP manager** polls agents and queries the MIB for SNMP agents on **UDP port 161** => **SNMP agent send any SNMP traps to the SNMP manager on UDP port 162**

## SNMP Operation

- **SNMP agents** => collect and store information about the device and its operation => **stored by the agent locally in the MIB** 

- **SNMP manager use get and set actions to perform operation** as seen in the table

![[Pasted image 20231226192239.png]]

*SNMP agent responds to SNMP Manager

- **Get an MIB variable** => Agent performs this function in response to a GetRequest-PDU from the network manager 

- **Set an MIB variable** => Agent performs function in response to SetRequest-PDU from the network manager => change the value and reply to SNMP manager with new settings in the device 

 ![[Pasted image 20231226193632.png]]
## SNMP Agent Traps

- NMS **polls SNMP agents on manage devices** => collect information to **monitor traffic loads**, **verify device configurations of managed devices** 

=> Periodic SNMP polling does have **disadvantages** => **delay between the time that event occurs and time it is noticed (via polling)**

=> Agent can send **traps** to inform the **NMS immediately of certain events** => **condition or event on the network** => for example **CPU utilization, improper user authentication, restarts, link status (up or down), MAC address tracking, closing of TCP connection, loss of connection to a neighbor and others**

=> Trap-directed notification **reduce network and agent resource by eliminating the need for some of SNMP polling requests**

![[Pasted image 20231227120039.png]]

![[Pasted image 20231227120100.png]]
## SNMP Versions

- **SNMPv1** : Simple Network Management Protocol => Full Internet Standard defined in **RFC 1157

- **SNMPv2c** : Defined in RFCs 1901 to 1908 => uses a community-string-based Administrative Framework

- **SNMPv3** : Interoperable standards-based protocol defined in RFCs 2273 to 2275 => **secure access by authenticating and encrypted packets over the network** => **Message integrity** to ensure packet not tampered in transmit => **Authentication** to determine the message is from a valid source and **Encryption** to prevent the content of a message from being read by **unauthorized source**

Cisco IOS software supports the **above three versions** => **Version 1 is legacy solution and not often encountered in networks today** 

=> SNMPv1 and SNMPv2c **use community-based form of security** => SNMPv2c use **bulk retrieval mechanism** and **more detailed error message reporting** to management stations => **retrieves table and large quantities of information** => **minimizing the number of round-trips** 

=> Conditions reported **through a single error code in SNMPv1**
=> **Error return codes in SNMPv2c** include **error type**

![[Pasted image 20231227123925.png]]
![[Pasted image 20231227123935.png]]
![[Pasted image 20231227123946.png]]
![[Pasted image 20231227123954.png]]
![[Pasted image 20231227124001.png]]

- SNMP Agent can **communicate with multiple SNMP manager using SNMPv1, SNMPv2c or SNMPv3
## Community Strings

- NMS **need access of MIB** => **authentication must be in place** => **community string on SNMPv1 and SNMPv2c which are plaintext password**  

- **Read-only (ro)** : provides **access to MIB variables => only read**
- **Read-write (rw) :** **Read and write access to all objects in MIB**

![[Pasted image 20231227124923.png]]
![[Pasted image 20231227124942.png]]
![[Pasted image 20231227125009.png]]
![[Pasted image 20231227125026.png]]

- Plaintext passwords are highly **vulnerable to man-in-the-middle attacks
## MIB Object ID

- **Organize variables hierarchically**
- Each variable as an **object ID (OID)**  => OID **uniquely identify managed objects in the MIB hierarchy** => **OID based on RFC standards  
- **Branches with variables common to many networking devices and some branches with variables specific to that device or vendor**

![[Pasted image 20231227125245.png]]

- Networking equipment **vendors like Cisco** define their **own private branches of the three to accommodate new variables**

## SNMP Polling Scenario 

- CPU statistics can **be compiled on NMS and graphed** => Threshold value **can be set** => Notification are sent **when exceeds this threshold 

![[Pasted image 20231227130425.png]]

=> Data retrieved via **snmpget utility** issued on NMS => To **manually retrieve** real-time data 

- Require SNMP version, correct community, IP address of the network device to query and OID number are set

![[Pasted image 20231227130914.png]]

## Introduction to Syslog

- Protocol to **notify administrator with detailed system messages** => either non-critical or significant => **variety of options** => storing, interpreting and displaying these messages

- **Syslog** protocol **developed for UNIX systems in 1980s** but first document as **RFC 3164 by IETF in 2001**  => Uses **UDP port 514** to send event notification messages **across IP networks** 

![[Pasted image 20231227144736.png]]

- Many networking devices support **syslog** => routers, switches, application servers, firewall and other network appliances => **Syslog protocol** allows to send their system messages across the network to **syslog servers**

*Several different syslog server software for Windows or UNIX (many freeware)

- **Gather logging information for monitoring and troubleshooting
- **Select type of logging information that is captured
- **Specify the destinations of captured syslog messages

## Syslog Operation

Cisco => **debug** enable syslog protocol to send system messages => can be send =>

- Logging buffer (RAM inside router or switch)
- Console line
- Terminal line
- Syslog server

![[Pasted image 20231227145341.png]]
## Syslog Message Format

![[Pasted image 20231227145431.png]]

**Each syslog level has its own meaning


- **Warning Level 4 - Emergency Level 0** : Software or hardware malfunctions => **functionality of the device is affected** => severity of the issue **determines the actual syslog level applied**

- **Notification Level 5** : Significant events => **interface up or down, system restart, etc

- **Information Level 6** : Not affect device functionality => %LICENSE-6-EULA_ACCEPT_ALL: The Right to Use End User License Agreement is accepted

- **Debugging Level 7 :**  From **debug** commands

## Syslog Facilities

- **Syslog also contain information on the facility** => facilities are service identifiers that **identify and categorize system state data for error and event message reporting** 

*Some common syslog message facility codes reported on Cisco IOS routers*

- **IF** : generated by an interface
- **IP** : generated by IP
- **OSPF** : generated by OSPF
- **SYS** : generated by device operating system
- **IPSEC** : generated by IP Security encryption protocol

By default => **format of syslog messages on Cisco IOS Software 

![[Pasted image 20231227150322.png]]

**Here an example**

![[Pasted image 20231227150423.png]]

*Facility = LINK*
*Severity level = 3*
*MNEMONIC = UPDOWN*
## Configure Syslog Timestamp

By default => **log message not timestamped** => command **service timestamps log datetime** => to force logged events to display date and time

![[Pasted image 20231227150836.png]]

## Router File Systems

- Cisco IOS File System (IFS) **allows administrator to navigate different directories and list the files in a directory** => **we can create subdirectories in flash memory or a disk** 

**show file systems** => lists all of available file system => see **amount of total and free memory, type of file system and its permissions** => **Read only (ro)**, **write only (wo) and read and write (rw)** 

![[Pasted image 20231227205952.png]]

=> flash file system has an **asterisk preceding it** => indicates **current default file system** => **bootable IOS is located in flash** => the **# indicates its a bootable disk** 
#### Flash File System

- Show contents of flash

![[Pasted image 20231227210557.png]]

**We can see the Cisco IOS file image in the end

#### NVRAM File System

![[Pasted image 20231227210742.png]]

=> Several **configuration file listed** and **startup-config file**

## Switch File Systems

![[Pasted image 20231227210902.png]]

## Use a Text File to Back Up a Configuration

- Using **Tera Term**

![[Pasted image 20231227211200.png]]
## Use a Text File to Restore a Configuration

- Configuration can be **copied from a file and pasted on a device** 

![[Pasted image 20231227211419.png]]

## Use TFTP to Back Up and Restore a Configuration

- Copies of configuration **should be stored as backup file in event of a problem** => can be store on **Trivial File Transfer Protocol (TFTP) server** or **a USB drive** 

Using **copy running-config tftp** or **copy startup-config tftp** 

![[Pasted image 20231227211800.png]]

![[Pasted image 20231227211917.png]]

## USB Ports on a Cisco Router

- **Universal Serial Bus (USB)** flash feature **provides an optional secondary storage capability and an additional boot device

![[Pasted image 20231227212208.png]]

![[Pasted image 20231227212329.png]]
## Use USB to Back Up and Restore a Configuration

- **show file systems** => to verify that USB drive is there and confirm the name

![[Pasted image 20231227212411.png]]

- **copy run usbflash0:/** to copy the configuration file to a USB flash drive => **the / is optional but indicates the root directory of the USB flash drive**

![[Pasted image 20231227212751.png]]

![[Pasted image 20231227212851.png]]

#### Restore Configurations with a USB Flash Drive

**copy usbflash0:/R1-Config running-config** => to restore a running configuration
## Password Recovery Procedures

- **Step 1.** Enter ROMMON mode
- **Step 2.** Change configuration register
- **Step 3.** Copy startup-config to the running-config
- **Step 4.** Change the password
- **Step 5.** Save the running-config as the new-startup-config
- **Step 6.** Reload the device

**Console access** => Terminal settings to access device are :

- 9600 baud rate
- No parity
- 8 data bits
- 1 stop bit
- No flow control
## Password Recovery Example

**Step 1.** Enter ROMMON mode

**Using Break sequence during the boot up 

![[Pasted image 20231227230335.png]]

**Step 2.** Change configuration register

**ROMMON software supports some basic commands such as confreg => **confreg 0x2142** => **ignore the startup config file during startup** 

**Enter Reset** to restart the device

![[Pasted image 20231227230606.png]]

**Step 3.** Copy startup-config to the running-config

![[Pasted image 20231227230636.png]]

**Step 4.** Change the password

![[Pasted image 20231227230711.png]]

**Step 5.** Save the running-config as the new-startup-config

- After new passwords are configured => change the configuration register back to **0x2102** with **config-register 0x2102** 

![[Pasted image 20231227230724.png]]

**Step 6.** Reload the device

![[Pasted image 20231227230733.png]]
## TFTP Servers as a Backup Location

- **Can be stored on a central TFTP server** => **to control the number of IOS images and revisions to those IOS images**

![[Pasted image 20231228162643.png]]

- **Uploads and downloads over the network** => The **network TFTP server can be another router, workstation or host system**

## Backup IOS Image to TFTP Server Example

- To **maintain network operations with minimum down time** => **necessary to have procedure in place for backing up Cisco IOS images** 

![[Pasted image 20231228163157.png]]

**Step 1. Verify image size in flash

![[Pasted image 20231228163241.png]]

**Step 2. Copy the image to the TFTP server**

![[Pasted image 20231228163328.png]]

## Copy an IOS Image to a Device Example

![[Pasted image 20231228163553.png]]

**Step 1. Verify image size in flash

![[Pasted image 20231228163241.png]]

**Step 2. Copy the image to the TFTP server**

![[Pasted image 20231228163632.png]]

## Boot System Command

- **To load the new image during bootup** => **boot system** command => **Save configuration**

![[Pasted image 20231228164007.png]]

=> During startup => **search of the configuration file in NVRAM for the boot system** command which specify the name and location of the Cisco IOS Software image to load

=> by default, router **load the first valid Cisco IOS image in flash

![[Pasted image 20231228164319.png]]
# ==XI. Network Design==

## Borderless Switched Network Design

- **Hierarchical** : Role of each device at every tier, simplifies deployment, operation and management => **reduce fault domains at every tier**

- **Modularity** : **Enablement on an on-demand basis**

- **Resiliency** : **Satisfies user expectations for keeping the network always on**

- **Flexibility** : **Allows intelligent traffic load sharing by using all network resources**

![[Pasted image 20231229140434.png]]

- **Two time-tested and proven hierarchical design frameworks for campus networks** => Three-tier layer and Two-tier layer models
### Three-Tier Model

![[Pasted image 20231229141020.png]]
### Two-Tier Model

![[Pasted image 20231229141038.png]]
## Access, Distribution and Core Layer Functions

#### Access Layer : 

- **When traffic enters or exits** the campus network => **to provide network access to the user** =>
- Access layer switches **connect to distribution layer switches** => implement **network foundation technologies such as routing, quality of service and security**

=> Next-generation switching platforms **provide more converged, integrated and intelligent services to various types of endpoints** 
#### Distribution Layer 

- Interfaces **between access layer and core layer** which provides :
	- **Aggregating large-scale wiring closet network**
	- **Aggregating Layer 2 broadcast domains** and **Layer 3 routing boundaries**
	- **Intelligent switching, routing and network access policy functions** => access the rest of the network
	- **High availability through redundant distribution layer switches to end user and equal cost paths to the core**
	- **Differentiated services to various classes of service applications at the edge of the network**
#### Core Layer

- **Network Backbone** => **Connect several layers of the campus network**
- **Aggregator for all of the distribution layer devices** and **ties the campus together with the rest of the network** 
- **Provide fault isolation and high-speed backbone connectivity**
## Three-Tier Example

- **Organizations where access, distribution and core are each separate layers**
- Centralized building location to all other buildings on the same campus => **simplified, cost-effective, efficient physical cable**

![[Pasted image 20231229142147.png]]

## Two Tier Example

- **When extensive physical or network scalability does not exist** 
- **When fewer users** 

=> Alternate **two-tier campus network design** => known as **collapsed core network**

![[Pasted image 20240103123936.png]]

## Role of Switched Networks

![[Pasted image 20240103124403.png]]

- Switched LAN => **allows flexibility, traffic management, quality of service and security** => **support for wireless networking and connectivity** => such as IP telephone and mobility services

## Design for Scalability

Scalability is the term for a network that **can grow without losing availability and reliability

- **Use expandable, modular equipment or clustered devices that can be easily upgraded to increase capabilities** => with modules => **Cluster can act as one device** => **simplify management and configuration**

- **Hierarchical network** => **creates separate access layer that can be expanded without affecting** the distribution and core layers on the campus network

- **Create IPv4 and IPv6 address strategy that is hierarchical** => **eliminates the need of re-address the network to support additional users and services**

- **Choose routers and multilayer switches** => **limit broadcasts and filter undesirable traffic from network** => Layer 3 devices filter and reduce traffic to the network core
### Redundant Links

- Implement **redundant links in the network** between **critical devices and between access layer and core layer devices**

![[Pasted image 20240103125406.png]]

### Multiple Links

- **Multiple links between equipment** => EtherChannel 
- **Combining multiple Ethernet Link** into a single => **load-balanced EtherChannel configuration** increases available bandwidth 
- **EtherChannel** => when budget restrictions prohibit purchasing high-speed interfaces and fiber runs

![[Pasted image 20240103125516.png]]
### Scalable Routing Protocol

- **Isolate routing updates** and **minimize size of the routing table**

![[Pasted image 20240103125841.png]]
### Wireless Connectivity

- **Allows mobility and expansion**

![[Pasted image 20240103125905.png]]

## Plan for Redundancy

- **Minimizing the possibility of a single point of failure** => failover services for critical devices
- Redundant path **offer alternate physical paths for data to traverse the network** => may cause logical Layer 2 loops => Spanning Tree Protocol (STP) **required

![[Pasted image 20240103125948.png]]

- **STP** eliminates Layer 2 loops => **disabling redundant paths in a switched network until the path is necessary** when failure occurs 

- Using Layer 3 in the backbone implement redundancy **without the need for STP at Layer 2** => **Layer 3** provides **best path selection** and **faster convergence during failover**
## Reduce Failure Domain Size

- **Limit size of the failure domains** => **area of the network impacted when critical device or network service experiences problems** 

#### Edge Router

![[Pasted image 20240103130652.png]]
#### AP1

![[Pasted image 20240103130729.png]]
#### S1

![[Pasted image 20240103130747.png]]
#### S2

![[Pasted image 20240103130838.png]]
#### S3

![[Pasted image 20240103130848.png]]

**Limiting the Size of Failure Domains

- Failure at core layer => **potentially large impact** 
- **Easiest** and **least expensive** to control size of failure domain in **distribution layer** => network errors can be contained **to smaller area** => **affecting fewer users** 

**Switch Block Deployment**

- Usually deployed in **pairs** => **access layer switches evenly divided between them** 
- **Failure of a single device** does **not cause the network to go down**

## Increase Bandwidth

- Link aggregation **such as EtherChannel** => **allows administrator to increase the amount of bandwidth between devices** by creating **one logical link made up of several physical links**

![[Pasted image 20240103131445.png]]

- **EtherChannel** can configure **load-balancing**

## Expand the Access Layer

- Expand with **Wireless connectivity**
- End devices require a **Wireless NIC 

![[Pasted image 20240103132106.png]]
## Tune Routing Protocols

 - With Advanced Routing protocols such **Open Shortest Path First (OSPF)**

![[Pasted image 20240103132317.png]]

- OSPF router **establish** and **maintain neighbor adjacencies** with other connected OSPF routers
- OSPF routers **synchronize their link-state database** => when network change => **Link-state updates are sent,** **informing** other **OSPF routers** of the change and **establish new best path** if available

## Switch Platforms

- **Campus LAN Switches** : eight fixed ports to hundreds of ports => Cisco 2960, 3560, 3850, 4500, 6500 and 6800 Series

![[Pasted image 20240103135437.png]]

- **Cloud-Managed Switches** : Enable virtual stacking of switches => monitor, configure thousands of switch ports over web

![[Pasted image 20240103183820.png]]

- **Data Center Switches** :  Promote infrastructure scalability, operational continuity, transport flexibility => Cisco Nexus Series switches

![[Pasted image 20240103183904.png]]

- **Service Center Switches :** aggregation switches and Ethernet access switches => **aggregation switches are carrier-grade** Ethernet switches that **aggregate traffic at the edge of the network** => Service provider Ethernet switches **features application intelligence, unified services, virtualization, integrated security and simplified management**

![[Pasted image 20240103184009.png]]

- **Virtual Networking :** Cisco Nexus virtual networking switch platforms provide **secure multi-tenant services** by adding **virtualization intelligence technology to the data center network**
## Switch Form Factors

- **Fixed configuration switches** : Features and options limited

![[Pasted image 20240103184442.png]]

- **Modular configuration switches** : accept field-replaceable line cards

![[Pasted image 20240103184508.png]]

- **Stackable configuration switches** : connect stackable switches that allow to operate as one large switch

![[Pasted image 20240103184550.png]]

- **Thickness** : Express in number of rack units 

![[Pasted image 20240103184559.png]]
## Port Density

- **Modular switches can support very high port densities** 
## Forwarding Rates

- **Processing capabilities** of a switch by rating **how much data the switch can process per second** => Entry-level switches have lower forwarding rates than enterprise-level =>

- **If forwarding rate is too low** => cannot accommodate **full wire-speed communication across all of its switch ports** 

Example : **48-port gigabit switch** operating at full wire speed **generates 48 Gbps of traffic** => if switch only **supports forwarding rate of 32 Gbps** => **can't run at full wire speed** across all ports simultaneously

Fortunately, access layer switches typically do not need to operate at full wire speed => **physically limited by their uplinks to distribution layer** => **More expensive for higher performing switches can be used at the distribution and core layers** where forwarding rate has greater impact on network performance
## Power over Ethernet

- **Deliver power to device over existing Ethernet cabling** => IP phone and wireless access points => **support PoE are expensive** 

## Multiplayer Switching 

- **Build routing table**
- Support **few routing protocols**
- **Forward IP packets** 

![[Pasted image 20240103190014.png]]
## Business Considerations for Switch Selection

![[Pasted image 20240103190037.png]]
## Router Requirements

- Use **network portion (prefix)** of the destination IP address to **route packets to a proper destination**
- **Select an alternate path if link goes down**
- **Provide broadcast containment by limiting broadcast to the local network**
- **Interconnect geographically separated locations**
- **Group users logically by application or department within an company** => **enhanced security** => **access control lists**

## Cisco Routers

- **Branch Routers** : highly available branch networks => fast recovery from typical faults while minimizing or eliminating the impact on service, provide simple network configuration and management for WAN infrastructures

*Cisco Integrated Service Router (ISR) 4000 Series Routers

![[Pasted image 20240103190727.png]]

- **Network Edge Routers** : high-performance, highly secure and reliable services => unite campus, data center and branch network

*Cisco Aggregation Service Routers (ASR) 9000 Series Routers*

![[Pasted image 20240103191632.png]]

- **Service Provider Routers** : deliver end-to-end scalable solutions and subscriber-aware services => optimize operations, reduce expenses and improve scalability and flexibility

*Cisco Network Convergence System (NCS) 6000 Series Routers*

![[Pasted image 20240103191945.png]]

- **Industrial** : Provide enterprise-class features in rugged and harsh environments => excellent for mission-critical application

*Cisco 1100 Series Industrial Integrated Services Routers*

![[Pasted image 20240103192048.png]]
# ==XII. Network Troubleshooting==

## Documentation Overview

**Common network documentation 

- **Physical** and **logical network topology diagrams**
- Network device documentation that records **all pertinent device information
- **Network performance** baseline documentation

=> Single location (hard copy and protected server)

## Network Topology Diagrams 

**Physical Topology**

- **Device name**
- **Device location (address, room number, rack location)
- **Interface and ports used**
- **Cable type**

![[Pasted image 20240104124229.png]]

**Logical IPv4 Topology**

- **Device identifiers**
- **IP addresses and prefix lengths**
- **Interface identifiers**
- **Routing protocols / static routes**
- **Layer 2 information (VLANs, trunks, EtherChannels)**

![[Pasted image 20240104124358.png]]

**Logical IPv6 Topology**

![[Pasted image 20240104124552.png]]
## Network Device Documentation

**Router Device Documentation**

![[Pasted image 20240104124646.png]]

**LAN Switch Device Documentation**

![[Pasted image 20240104124741.png]]

**End-system Documentation Files**

![[Pasted image 20240104124839.png]]

## Establish Network Baseline

**Step 1 - Determine What Types of Data to Collect**

- Interface utilization and CPU utilization

**Step 2 - Identify Devices and Ports of Interest**

- **Network device ports that connect to other network devices**
- **Servers**
- **Key users**
- **Anything else considered critical to operations**

![[Pasted image 20240104125330.png]]

**Step 3 - Determine the Baseline Duration**

**two-to-four week baseline adequate

![[Pasted image 20240104125420.png]]
## Data Measurement

- Most common Cisco IOS commands used for data collection

![[Pasted image 20240104125806.png]]

**Sophisticated network management software is typically used to baseline large and complex networks**
## General Troubleshooting Procedures

![[Pasted image 20240104130206.png]]
## Seven-Step Troubleshooting Process

![[Pasted image 20240104130240.png]]

- **Define the Problem**

- **Gather Information**

- **Analyze Information**

- **Eliminate Possible Causes**

- **Propose Hypothesis**

- **Test Hypothesis**

- **Solve the problem**
## Question End Users

Network problem initially **reported by End user => information vague or misleading**  

![[Pasted image 20240104130852.png]]
## Gather Information

![[Pasted image 20240104131906.png]]
## Troubleshooting Layered Models

OSI and TCP/IP models => **begin by the first layer** first

Although **routers and multilayer switches** make **forwarding decisions at Layer 3** and can make filtering decisions using **Layer 4 with ACL**

![[Pasted image 20240104132009.png]]
## Structured Troubleshooting Methods

**Bottom-UP** => start with physical layer

- *Most networking problems reside at lower levels*

![[Pasted image 20240104132322.png]]

**Top-Down** => start with Application Layer

![[Pasted image 20240104132616.png]]

**Divide-and-Conquer** => guess which OSI layer to start your investigation

![[Pasted image 20240104132843.png]]

**Follow-the-Path** => Most basic troubleshooting techniques => discovers the traffic path all the way from source to destination

**Substitution** => Single point of failure => border router goes down => change devices and restore service rather than troubleshoot the issue

**Comparison** => Resolve problem by changing the nonoperational elements to be consistent with the working ones => compare configuration, software versions, hardware, device properties, links => **lead to not clearly revealing the root cause of the problem**

**Educated Guess** => Rely on extensive knowledge and experience to isolate and solve network issues

## Guidelines for Selecting a Troubleshooting Method

![[Pasted image 20240104133431.png]]
## Software Troubleshooting Tools

- **Network Management System Tools** (NMS)  
- **Knowledge Bases**
- **Baselining Tools** 
## Protocol Analyzers

**Wireshark** => includes physical layer bit data, data link layer information, protocols and descriptions for each frame

![[Pasted image 20240104134044.png]]

## Hardware Troubleshooting Tools

- **Digital Multimeters** => voltage, current and resistance
- **Cable Testers** => detect broken cable
- **Cable Analyzers** => test and certify copper and fiber cables for different services and standards
- **Portable Network Analyzers** => Troubleshooting switched networks and VLANs
- **Cisco Prime NAM** => Hardware and software for performance analysis in switching and routing => embedded brower-based interface

![[Pasted image 20240106083737.png]]
## Syslog Server as a Troubleshooting Tool

- IP device known as syslog client **send text-based log messages** to another IP device the syslog server => Syslog RFC 5424

Event messages can be **sent to one or more** of the following solutions :

- **Console** => logging by default while connected to the **console port device

- **Terminal lines** => Enabled EXEC sessions received log messages but **not stored in device

- **Buffered logging** => Log messages can be **stored in memory for a time** => **log messages cleared when device rebooted**

- **SNMP traps** => Send to an external SNMP server **with SNMP traps

- **Syslog** => Send to log messages to an **external syslog service** => this service can be on servers or workstations => **most popular message logging facility

![[Pasted image 20240106084553.png]]

=> log by default include **all messages from level 0 to 7** to console => **logging trap** level command limits messages logged to syslog based on **severity** 

![[Pasted image 20240106084739.png]]
## Physical Layer Troubleshooting

![[Pasted image 20240106085251.png]]

![[Pasted image 20240106085311.png]]

![[Pasted image 20240106090329.png]]
## Data Link Layer Troubleshooting

![[Pasted image 20240106102655.png]]

![[Pasted image 20240106102913.png]]

![[Pasted image 20240106102950.png]]
## Network Layer Troubleshooting

![[Pasted image 20240106103241.png]]

![[Pasted image 20240106103256.png]]
## Transport Layer Troubleshooting ACLs

![[Pasted image 20240106103530.png]]

![[Pasted image 20240106103608.png]]
![[Pasted image 20240106104208.png]]
## Transport Layer Troubleshooting NAT for IPv4

![[Pasted image 20240106104242.png]]
![[Pasted image 20240106104256.png]]
## Application Layer Troubleshooting

![[Pasted image 20240106104629.png]]
![[Pasted image 20240106104813.png]]
## Components of Troubleshooting End-to-End Connectivity

- **PC1 can't access SRV1** 
- **Configuration uses SLAAC with EUI-64**

![[Pasted image 20240106120655.png]]

**Step 1.** Check physical connectivity when network communication stops => **cables and hardware**

**Step 2.** Check **duplex mismatches**

**Step 3.** Check **data link and network layer addressing** => **IPv4 ARP tables, IPv6 neighbor tables**, **MAC address tables** and **VLAN assignments** 

**Step 4.** **Verify default gateway** is correct

**Step 5.** Determining the **correct path from source to destination** => check routing information

**Step 6.** Verify **transport layer** => **telnet can be used to test transport layer connections**

**Step 7.** Verify **there are no ACLs blocking traffic**

**Step 8.** Ensure **DNS settings are correct** 
## End-to-End Connectivity Problem Initiates Troubleshooting

![[Pasted image 20240106121306.png]]

- IPv4 ping 

![[Pasted image 20240107084443.png]]

- IPv4 traceroute

![[Pasted image 20240107084747.png]]

- IPv6 ping and traceroute

![[Pasted image 20240107084822.png]]

## Step 1. Verify Physical Layer

- **CPU, RAM** and **storage device** allow device to **boot and run the operating systems and interfaces

Commands to **verify operation of these generic components

- **show processes cpu**
- **show memory**
- **show interfaces**

![[Pasted image 20240107085345.png]]

**Input queue drops** 

=> indication of **CPU cannot process packet in time** => **not necessarily indicate a problem during peak periods drops are normal** => if number **consistently high** => try to spot at **which moments and how it's related to CPU

**Output queue drops**

=> packet dropped due **congestion on interface** => normal **when when aggregate input traffic is higher than output traffic** => application are **sensitive to those such as VoIP** => **need to implement advanced queuing mechanism QoS** 

**Input errors**

=> indicate errors **experienced during reception of the frame** such as CRC errors => could **indicate cabling problems**, interface hardware problems => **Ethernet-based network**, **duplex mismatches**

**Output errors** 

=> indicate **collisions** when duplex mismatches => in **full-duplex transmission no collisions occur**  

## Step 2. Check Duplex Mismatches

- Autonegotiation of **speed and duplex**
- **If autonegotiation fails, manually set speed and duplex on interconnecting ends**
- **Point-to-point Ethernet links always full-duplex**
- **Half-duplex is uncommon and encountered only when legacy hubs**

**Troubleshooting Example**

![[Pasted image 20240107091525.png]]

- Console message on **switch S2**

![[Pasted image 20240107092951.png]]

- Interface **full-duplex**

![[Pasted image 20240107093102.png]]

- Interface **half-duplex** => put on **duplex auto**

![[Pasted image 20240107093142.png]]

## Step 3. Verify Addressing on the Local Network

 **Verify mapping between destination IP addresses and Layer 2 Ethernet addresses**

- **Windows IPv4 ARP Table**

![[Pasted image 20240107094725.png]]

- **Windows IPv6 Neighbor Table**

![[Pasted image 20240107094845.png]]

- **IOS IPv6 Neighbor Table**

![[Pasted image 20240107094951.png]]

- **Switch MAC Address Table**

![[Pasted image 20240107095020.png]]
## Troubleshoot VLAN Assignment Example

**Check ARP Table** on PC1

=> default gateway **10.1.10.1** not here

![[Pasted image 20240107095603.png]]

**Check Switch MAC Table**

MAC addresses for R1 is on different VLAN **than the rest of the 10.1.10.0/24 devices

![[Pasted image 20240107095701.png]]

**Correct VLAN Assignment**

![[Pasted image 20240107095810.png]]
## Step 4. Verify Default Gateway

![[Pasted image 20240107095940.png]]

**R1 Routing Table** 

![[Pasted image 20240107100013.png]]

**PCA Routing Table**

![[Pasted image 20240107100024.png]]

## Troubleshoot IPv6 Default Gateway Example

**R1 Routing Table**

![[Pasted image 20240107102404.png]]

**PC1 Addressing**

Link-local address automatically created by device => **missing IPv6 global unicast address** and **IPv6 default gateway**

![[Pasted image 20240107102503.png]]

**Check R1 Interface Settings**

We have to check if R1 router are a **member of the All-IPv6-Routers multicast group FF02::2

Here we can see it's note the case => R1 **not sending out ICMPv6 RAs on this interface**

![[Pasted image 20240107103139.png]]

**Correct R1 IPv6 Routing**

=> With **ipv6 unicast-routing** => enable **R1 to be member of ff02::2 => All-IPv6-Routers multicast group**

![[Pasted image 20240107103204.png]]

**Verify PC1 has IPv6 Default Gateway**

![[Pasted image 20240107103415.png]]
## Step 5. Verify Correct Path

![[Pasted image 20240107103505.png]]

**R1 IPv4 Routing Table** 

![[Pasted image 20240107103538.png]]

**R1 IPv6 Routing Table**

![[Pasted image 20240107103556.png]]

IPv4 and IPv6 routing tables populated :

- **Directly connected networks**
- **Local host or local routes**
- **Static routes**
- **Dynamics routes**
- **Default routes**

=> process of forwarding process based **on longest bit match or longest prefix match

![[Pasted image 20240107105324.png]]

## Step 6. Verify Transport Layer 

*Access to HTTP fail

**Troubleshooting Example**

![[Pasted image 20240107105927.png]]

**Network layer and all layers below are operational

![[Pasted image 20240107110112.png]]

Success

![[Pasted image 20240107110157.png]]

**Successful transport layer connection but R2 refusing the connection using port 80**

## Step 7. Verify ACLs

![[Pasted image 20240107110326.png]]

- **show ip access-lists** displays ACL configured correctly

![[Pasted image 20240107110424.png]]

- Verify which interface has the ACL applied => **show ip interfaces serial 0/1/1** and **show ip interfaces gig 0/0/0** 

![[Pasted image 20240107110612.png]]

=> ACL **applied on G0/0/0**, it **should be applied to serial 0/1/1**

**Correct the Issue** 

![[Pasted image 20240107110745.png]]
## Step 8. Verify DNS

You can **enter names to be used instead of the IPv4 address of the switch or router** 

![[Pasted image 20240107111011.png]]

![[Pasted image 20240107111020.png]]

**nslookup** to check name-to-IP-address mapping 

# ==XIII. Network Virtualization==

## Cloud overview

**Cloud computing**

- Access data anywhere / any time
- Subscribe only to needed services
- Eliminates or reduces IT equipment, maintenance and management
- Reduces cost, energy, physical plant requirements and personnel training 
- Rapid responses to increasing data volume 

**pay-as-you-go**
## Could Services

**Software as Service** (SaaS) =>  access to applications and services such **Office 365** 

**Platform as a Service** (PasS) => access to developement tools and services used to deliver the application => users are programmers 

**Infrastructure as a Service**(IaaS) => access network equipment, virtualized network services => deploy and run software code 

**ITaaS** => support for each of the cloud service => on demand and delivered

![[Pasted image 20240108145031.png]]
## Cloud Models

- **Public clouds** => Available to general population => may be **free or offered** on a **pay-per-user model such as paying for online storage** => public cloud **used internet to provide services

- **Private clouds** => for **specific organization or entity** such government => **expensive to build and maintain using organization's private networks** => **could be managed by outside organization with strict access security**  

- **Hybrid clouds** => made of **up of two or more clouds** => where each **parts remains separate object** => but connected **using single architecture** => **degrees of access to various services based on user access rights**

- **Community clouds** => exclusive use **by a specific community** => healthcare organizations must remain compliant with policies and laws => **require special authentication and confidentiality**

![[Pasted image 20240108145915.png]]
## Cloud Computing Vs Data Center

- **Data center** : Data storage and processing facility run by an in-house IT department or leased offsite

- **Cloud computing** : off-premise service that offers on-demand access to a shared pool of configurable computing resources => can be rapidly provisioned and released with minimal management effort

=> data center occupy one room of a building => **very expensive to build and maintain** 
=> Smaller organizations cannot afford to maintain their own private data center => they **leased server and storage services**
## Cloud Computing and Virtualization

Virtualization => **separates operating system (OS) from hardware** => cloud services **dynamically provision servers** with compute resources they need 
## Dedicated Servers

![[Pasted image 20240108155237.png]]

- Major problem => when **component fails server becomes unavailable => single of failure**

=> when **dedicated servers unused for long periods of time** => **wasted of energy** 
## Server Virtualization

- Take advantage of **idle resources** and **consolidates number of required servers** => **multiples operating system to exist on a single hardware platform**

*Hypervisor OS Installation*

![[Pasted image 20240108155625.png]]

=> Virtualization **includes redundancy** from a **single point of failure** => VM can be **restarted on another hypervisor** => **same VM can run on two hypervisors concurrently** copying RAM and CPU instructions between them


=> **If one hypervisor fails** => VM continues **running on the other hypervisor** => services running on VM are also virtual and **can be dynamically installed or uninstalled as needed


=> Hypervisor is a **program, firmware or hardware that adds abstraction layer on top of the physical hardware** 

=> Create virtual machines that have **access to all hardware of the physical machine such as CPUs, memory, disk controllers and NICs**

## Advantages of Virtualization

Major advantage

- **Less equipment is required** 
- **Less energy is consumed**
- **Less space required** 

Additional benefits of virtualization

- **Easier prototyping** 
- **Faster server provisioning**
- **Increased server uptime**
- **Improved disaster recovery**
- **Legacy support**
## Abstraction Layers

**Computer system consisting of following abstraction layers**

- **Services
- **OS**
- **Firmware**
- **Hardware**

![[Pasted image 20240108160631.png]]

**Hypervisor can support multiple instances of OS**

![[Pasted image 20240108160711.png]]
## Type 2 Hypervisors

**Hosted Hypervisors** => hypervisor installed **on top of the existing OS** such as **macOS, Windows or Linux** 

![[Pasted image 20240108160929.png]]

**Common Type 2 hypervisors 

- Virtual Pc
- VMware Workstation
- Oracle VM VirtualBox
- VMware Fusion
- Mac OS X Parallels
## Type 1 Hypervisors

**Bare metal** => Hypervisor installed directly on hardware => **used on enterprise servers and data center networking devices**

=> Type 1 hypervisors **direct access to hardware resources** => **more efficient than hosted architectures** => **improve scalability, performance and robustness**

![[Pasted image 20240108161530.png]]
## Installing VM on Hypervisor

- Type 1 hypervisors require **management console** => used to manage **multiple servers** => can **automatically consolidate servers and power on off servers as required** 

**Here Server 1 become low on resources** => to **make more resources available**=> management console can be use to **move the Windows instance to the hypervisor on Server2** => can be **programmed with thresholds that trigger** the move automatically

![[Pasted image 20240108163146.png]]

- Cisco Unified Computing System (UCS) Manager => controls **multiple servers and manages resources for thousands of VMs**
## The Complexity of Network Virtualization

Server virtualization hides **server resources** such as **number and identity of physical servers, processors and OSs** from servers users

- Create Problem => Virtual LANs (VLANs) used by **VMs must be assigned to the same switch port as the physical server running the hypervisor** => VM are **movable** and **network administrator must be able to add, drop and change network resources and profiles**

![[Pasted image 20240108164227.png]]

**Dynamic ever-changing traffic requires a flexible approach to network resource management** 
## Control Plane and Data Plane

- **Control Plane** : brains of a device => **make forwarding decisions** => **control plane contains Layer 2 and Layer 3 route forwarding mechanisms** such as routing protocol neighbor tables and topology tables, IPv4 and IPv6 routing tables, STP and ARP table => information sent to the control plane is processed by the CPU

- **Data Plane** : Also called **forwarding plane** => used to **forward traffic flows** => Routers and Switches use **information form the control plane to forward incoming traffic out the appropriate egress interface** => **Information in data plane processed by a special data plane processor without CPU getting involved** 

![[Pasted image 20240108173242.png]]

**Cisco Express Forwarding (CEF)** uses **control plane and data plane to process packets**

=> CEF is an **advanced Layer 3 IP switching technology** enables forwarding of packets to **occurs at the data plane without consulting the control plane** 

**In CEF,** control plane routing table pre-populates the **CEF Forwarding Information Base (FIB)** in the data plane

**Control plane's ARP table pre-populates the adjacency table** => packet are **forwarded directly by data plane based on information contained in the FIB and adjacency table** without needing to consult the information in the control plane

=> **SDN** is the **separation of control and data plane** => **control plane function is removed from each device and performed by centralized controller** 

=> Centralized controller => **communicates control plane function to each devices** => each devices **focus on forwarding data while centralized controller manager data flow, increases security**

![[Pasted image 20240108183120.png]]

**Management Plane**

- Responsible for managing device through connection to the network

Secure Shell (SSH), Trivial File Transfer Protocol (TFTP), Secure FTP, Secure Hypertext Transfer Protocol (HTTPS) and Simple Network Management Protocol (SNMP) to access **management plane and configure a device** 
## Network Virtualization Technologies

VMware developed **virtualizing technology** over a decade ago => **support host OS to support one or more client OS**

#### Two Major Network Architectures have been developed to support network virtualization

- **Software-Defined Networking (SDN)** : Network Architecture that virtualizes network, offering a new approach to network administration and management to simplify and streamline the administration process

- **Cisco Application Centric Infrastructure (ACI)** : pupose-build hardware solution for integrating cloud computing and data center management

#### Components of SDN

- **OpenFlow** : developed for Stanford University to **manage traffic between routers, switches, wireless access points and controller** => **basic element in building SDN solutions** 

- **OpenStack** : Approach of **virtualization and orchestration** platform designed **to build scalable cloud environments and provide an IaaS solution** => Often used with Cisco ACI => Orchestration in network is the **process of automating the provisioning of network components such as servers, storage, switches, routers and applications** 

- **Other components** : Include interface to the Routing System (I2RS), Transparent Interconnection of Lots of Links (TRILL), Cisco FabricPath (FP) and IEEE 802.1aq Shortest Path Bridging (SPB)

![[Pasted image 20240108183836.png]]
## Traditional and SDN Architectures

![[Pasted image 20240108184854.png]]

SDN controller **is a logical entity** that **enables network administrators to manage and dictate how data plane of switches and routers should handle network traffic** => **Orchestrates, mediates and facilitates communication between applications and network elements** 

![[Pasted image 20240108185349.png]]

- **Use of Application Programming Interfaces (APIs) within SDN framework

- **API** is a set of **standardized requests** that define **proper way for an application to request services** from another application

- **SDN controller uses northbound APIs to communicate with upstream applications** => to shape traffic and deploy services

- **SDN controller uses southbound APIs to define behavior of data planes on downstream switches and routers** => OpenFlow is original and widely implemented southbound API
## SDN Controller and Operations

- Defines **data flows between centralized control plane and data planes on individual routers and switches**

- Each flow traveling **through the network must first get permission from the SDN controller** => verifies the communication is permit according **to network policy** => If the controller **allows a flow**, **computes a route for the flow to take and adds an entry for that flow** in each the switches along the path

- **Complex function performed by controller** => **controller populates flow tables** => SDN controller communicates with OpenFlow-compatible switches using **OpenFlow protocol** => use of **Transport Layer Security (TLS) to securely send control plane communications over the network** 

=> **Each OpenFlow switch connects to other OpenFlow switches** => connect end-user devices that are part of a packet flow

![[Pasted image 20240108191006.png]]

- **Flow Table** : This table **matches incoming packets to a particular flow** and **specified the functions that are to be performed on the packet** => may be **multiple flow tables that operate in a pipeline fashion

- **Group Table** : Flow table **may direct flow to a Group Table** which may **trigger a variety of actions that affect one or more flows**  

- **Meter Table** : **Triggers a variety of performance-related actions on flow including the ability to rate-limit the traffic
## Core Components of ACI

Three core components of the ACI architecture

- **Application Network Profile (ANP)** : ANP is **collection of end-point groups (EPG)** , their **connections and the policies** - that define those connections => such as **VLANs, web services and applications** 

- **Application Policy Infrastructure Controller (APIC) :** Considered to be the **brain of the ACI architecture** => is centralized software **controller that manages** and operates a **scalable ACI clustered fabric**  => 

- **Cisco Nexus 9000 Series switches** : These switches provide **application-aware switching fabric** and **work with an APIC** to manage the virtual and physical network infrastructure

![[Pasted image 20240108193841.png]]
## Spine-Leaf Topology

Composed of **APIC** and **Cisco Nexus 9000 series switches using two-tier spine-leaf topology** 

- **Leaf attach to the spine** but **never attach to each other**
- In Spine-Leaf **everything is one hop from everything else
- Cisco APICs and **all other device in the network physically attach to leaf switches**
- **Compara to SDN** => **APIC controller** does not manipulate data path directly => APIC **centralizes policy definition and programs the leaf switches to forward traffic based on defined policies**

![[Pasted image 20240109103030.png]]
## SDN Types

**Cisco Application Policy Infrastructure Controller - Enterprise Module (APIC-EM)** => extends ACI aimed at enterprise and campus deployments

**Three types of SDN**

- **Device-based SDN** : Devices are programmable by **applications running on device itself** or **server in the network** => **Cisco OnePK** example of a device-based SDN => **enables programmers to build applications using C, Java with Python to integrate and interact with Cisco devices**

![[Pasted image 20240109112518.png]]

- **Controller-based SDN** : SDN use **centralized controller** that has **knowledge of all devices in the network** => **applications can interface with the controller responsible for managing devices and manipulating traffic flows throughout the network** => Cisco Open SDN Controller is a **commercial distribution of OpenDaylight**

![[Pasted image 20240109112712.png]]

- **Policy-based SDN** : Similar to controller-based SDN where **centralized controller has a view of all devices in the network** 

=> **Policy-based SDN** include **additional Policy layer** that **operates at a higher level of abstraction** => uses **built-in applications that automate advanced configuration tasks** via guided workflow and user-friendly **GUI** => no programming skills required => **Cisco APIC-EM** is an example of this type of SDN

![[Pasted image 20240109113020.png]]

Each type of SDN **has its own features and advantages** => Policy-based SDN **is the most robust** providing **simple mechanism to control and manage policies across the entire network** 

Cisco APIC-EM => provide a **single interface for network management** 

- Discovering and accessing device and host inventories
- viewing the topology 
- tracing the path between end points
- settings policies

![[Pasted image 20240109113433.png]]
## APIC-EM Path Trace

- **Easily visualize traffic flows and discover any conflicting, duplicate or shadowed ACL entries**
- This tool **examines specific ACLs on path** between two end nodes => **network administrator can now make adjustments**

![[Pasted image 20240109113708.png]]
# ==XIV. Network Automation==

## Increase in Automation

Automation is **process that is self-driven => reduces and potentially eliminates need for humain intervention**

=> Confined by **manufacturing industry** => **Highly repetitive tasks** such as **automobile assembly** 

- **Machine work 24 hours without breaks**
- **Provide more uniform product**
- **Vast amounts of data that can be quickly analyzed to provide information which can help guide an event or process**
- **Can be used in dangerous conditions such as mining, firefighting, cleaning up industrial accident** 
- **Smart devices can reduce energy usage** make a medical diagnosis and **improve automobile driving safety
## Thinking Devices

Smart appliance => **lowering its power consumption during periods of peak demand** or **as complex as a self-driving car**

To "think" => **need to be programmed using network automation tools
## Data Formats Concept

- A well-designed form is **dictated by making information the easier for the intended audience to understand** 
- Data formats **way to store** and **exchange data in structured format** => Hypertext Markup Language (HTML) is a **standard markup language** for describing **the structure of web pages

![[Pasted image 20240109134120.png]]

Common data formats **used for network automation and programmability**

- **JavaScript Object Notation (JSON)**
- **eXtensible Markup Language (XML)**
- **YAML Ain't Markup Language (YAML)**

Data format **selected will depend on format used by application, tool or script you are using** => many systems support **more than one data format** => allows user to choose their preferred one
## Data Format Rules

- Syntax **includes types of brackets such as [], (), {}, the use of white place or indentation, quotes, commas and more
- How objets are represented => **characters, strings, lists and arrays**
- How key/value pairs **represented** => usually **on the left side** and **identifies** or **describes the data** => the value on the right **is the data itself and can be character, string, number, list or another type of data**

Example :

**open notify ISS location now** => that tracks the current location of the **International Space Station** => simple **Application Programming Interface (API)** call to server => returns current latitude and longitude of the space station along with UNIX timestamp => returned using **JavaScript Object Notation (JSON)** 

![[Pasted image 20240109135435.png]]

JSONView => **browser extension** that allow you to **view JSON in more readable format** 

**Data objects are displayed in key/value pairs** => easier to interpret => **remove the quotation marks** from the key

![[Pasted image 20240109135707.png]]
## Compare Data Formats

### JSON Format

![[Pasted image 20240109135925.png]]
### YAML Format

![[Pasted image 20240109135951.png]]
### XML Format

![[Pasted image 20240109135958.png]]
## JSON Data Format

Human readable data format used by application for **storing, transferring and reading data** 

=> JSON very popular format used **by web services and API to provide public data** => easy to parse and can be used with **most modern programming languages including Python**
### IOS Router Output

**show interface GigabitEthernet0/0/0**

![[Pasted image 20240109140229.png]]

=> this information can be represented in **JSON Format** => notice **each object (each key/value pair)** is a different piece of data **about the interface including its name, description and whether the interface is enabled**

### JSON Output

![[Pasted image 20240109140427.png]]
### JSON Syntax Rules

- hierarchical structure and contains nested values
- used **braces {} to hold objects** and **square brackets [] hold arrays**
- Data written as **key/value pairs**

**Data known as an object is one or more key/value pairs enclosed in braces {} 

- **Keys** must be strings within **double quotation marks " "**
- Values must be valid JSON data type **(string, number, array, Boolean, null or another object)**
- Keys and values are **separated by a colon**
- Multiple key/value pairs **within an object are separated by commas** 
- Whitespace is not **significant** 

**Keys must contain more than one value** => known as an array => ordered list of values

- Key followed **by a colon and a list of values enclosed in square brackets [ ]**
- Array **ordered list of values**
- Array can contain **multiple value types including string, number, Boolean, object or another array inside the array** 
- Each value **in the array is separated by a comma** 
### JSON List of IPv4 Addresses

![[Pasted image 20240109141322.png]]

## YAML Data Format 

Another type of humain readable data format used by **applications for storing, transferring and reading data** 

- It's like JSON considered **superset of JSON
- **Minimalist format making it easy to both read and write**
- Use **indentation to define its structure without use of brackets or commas** 
### JSON for GigabitEthernet2

![[Pasted image 20240109141547.png]]
## YAML for GigabitEthernet2

Key value pairs **separated by a colon without the use of quotation marks** => YAML a **hyphen is used to separate each element in a list** 

![[Pasted image 20240109141656.png]]
## XML Data Format

Another type of humain readable data format used by **applications for storing, transferring and reading data** 

- **It's like HTML** 
- **It's self-descriptive** => encloses data within related **set of tags** <tag>data</tag>
- Unlike HTML => XML uses **no predefined tags or document structure** 
### XML for GigabitEthernet2

![[Pasted image 20240109142207.png]]
## API Concept

API => **software that allows other applications to access its data or services** 

- Set of rules **describing how one application can interact with another** and **instructions to allow the interaction to occur
- User send **API request to a server** asking specific information <=> **receives an API responses in return from the server** with requested information

API similar as a **waiter in a restaurant** => customer in a **restaurant would like to have some food delivered to the table** => Take order and tells the kitchen what to do => waiter will then deliver the food back to the customer

![[Pasted image 20240109143932.png]]
## An API Example

First option => web site of a specific airline to book an flight => user enter **information to make a reservation request** => **interacts directly with airline's own database and provide user information matching user request**

![[Pasted image 20240109144012.png]]

In the second option => **Travel service web site interacts with various airline databases using APIs provided by each airline** 

![[Pasted image 20240109144325.png]]

API **act** as a **messenger** between **requesting application** and **the application** on the server that **provides data or service** => the message from the requesting application to the server where the data resides is known **API call**
## Open, Internet and Partner APIs

- **Open APIs or Public APIs** : Publicly available can be used **with no restrictions** => **International Space Station API** is a example of **Public API** => such as **Google Maps** require **user to get a free key or token** prior to use the API  

- **Internal or Private APIs** : Used **by organization or company to access data and services for internal use only** => Private API is allowing **authorized salepeople access to internal sales data on their mobile devices

- **Partner APIs** : Use **between a company and its business partners or contractors to facilitate business between them** => business partner **must have a license or other form of permission to use the API** => Travel Service using an **airline's API** example of partner API

![[Pasted image 20240109145117.png]]
## Types of Web Service APIs

Web service is a **service** available **over internet** using **World Wide Web** => **Four types of web service APIs

- Simple Object Access Protocol (SOAP)
- Representational State Transfer (REST)
- eXtensible Markup Language-Remote Procedure Call (XML-RPC)
- JavaScript Object Notation-Remote Procedure Call (JSON-RPC)

![[Pasted image 20240109145356.png]]

- **SOAP** designed **by Microsoft in 1998** => **messaging protocol for exchanging XML-structured information** often over **HTTP or Simple Mail Transfer Protocol (SNMP)** => API **considered slow to parse, complex and rigid**

- **REST API framework** does not require XML => **REST uses HTTP, less verbose and easier to use than SOAP** => refers to the style of **software architecture and become popular due to its performance, scalability, simplicity and reliability**

REST widely used in **web service API** => accounting **over 80% of all API types used** 

RPC is when **one system request that another system executes some code and returns information** => done **without understand details of the network** =>  works like **REST API** but there are **differences dealing with formatting and flexibility**

=> **XML-RPC** is a **protocol developed prior to SOAP** => later evolved into became SOAP

=> **JSON-RPC** **very simple protocol** and **similar to XML-RPC**
## REST and RESTful API

![[Pasted image 20240109151440.png]]

REST is **architectural style for designing web service applications** => style of web architecture that has **many underlying characteristics** and **governs the behavior of clients and servers** 

REST API is an **API** that **works on TOP of the HTTP protocol** => defines a **set of functions developers can use** to **perform requests and receive responses via HTTP protocol** such as **GET and POST**

*API can be considered as RESTful if it has the following features

- **Client-Server** : client **handles the front end** and the server **handles the back end** 
- **Stateless** : no client data is **stored on the server between requests** **Session stored on client**
- **Cacheable** : Clients **cache responses to improve performance**
## RESTful Implementation

RESTful web service implemented **using HTTP** 

- Base Uniform Resource Identifier (URI) **for the web service** 
- Data format **supported by the web service JSON, YAML OR XML** but could be any other data format
- **Set of operations supported** by the web service using **HTTP methods
- **API** must be **hypertext driven**

![[Pasted image 20240109152709.png]]

- HTTP **request ask for JSON-formatted data** => **if request is successfully constructed according to the API documentation** => the server will **respond with JSON data** 

**JSON data** can be **used** by client's web application to **display the data** 

Example :  smartphone mapping app show the location of San Jose, California

![[Pasted image 20240109152752.png]]
## URI, URN and URL

**Web resources and web services** such as **RESTful APIs** identified **using a URI**  => URI is a **string of characters that identifies a specific network resource** 

*URI has two specializations*

- **Uniform Resource Name (URN) :** identifies only the namespace of the resource (web page, document, image) without reference to the protocol

- **Uniform Resource Location (URL) :** defines network location of a specific resource on the network => **HTTP or HTTPS URLs** use in web browsers => FTP, SFTP, SSH can use a URL => URL using SFTP look like sftp://sftp.example.com

- **Protocol/scheme** : HTTPS or other protocols such as FTP, SFTP, mailto and NNTP*
- **Hostname :** www.example.com
- **Path and file name :** /author/book.html
- **Fragment :** #page155

![[Pasted image 20240109153049.png]]
## Anatomy of a RESTful Request

### Parts of an API Request

![[Pasted image 20240109160325.png]]
### Partial JSON Payload Received from API Request

**show part of the API response** => MapQuest directions from San Jose to Monterey in JSON format

![[Pasted image 20240109160533.png]]

**Different parts of the API request**

- **API Server** : URL for the server that answers REST requests => Mapquest API server
- **Resources** : Specifies the API that being requested => MapQuest direction API
- **Query** : Specifies the data format and information the client is requesting from the API service :
	- **Format** : usually JSON but can be YAML or XML => here JSON requested
	- **Key** : key for authorization =>need to replace "KEY" with a valid key to submit a valid request
	- **Parameters** : send information pertaining to the request => include directions the API need> **"from=SAN+Jose,Ca" and "to=Monterey,Ca"

**Many RESTful APIs** => including public APIs require a key => **identify the source of the request** 

- authenticate the source **to make sure they are authorized to use the API**
- **limit number of people using the API**
- **limit number of requests per user**
- **better capture and track the data being requested by users**
- **gather information on the people using API**
## RESTful API Applications

**RESTful API request** can be made in other ways
### Developer Web Site

Allow user to perform API request within the developer web page by entering in the parameters 

![[Pasted image 20240109163147.png]]
### Postman

- Application for testing and using **REST APIs** => for **constructing and sending REST API requests** => with query **parameters and keys** 

- **Allow to collect and save frequently used API calls in history**

- **Excellent tool for learning how to construct API requests** => for analyzing data returned from an API

![[Pasted image 20240109163257.png]]
### Python

API can be called **within Python program** => possible **automation, customization and App integration of the API** 

![[Pasted image 20240109163541.png]]
### Network Operating Systems

Using **protocol such as NETCONF (Net CONFiguration)** => network operating systems are **beginning to provide an alternative method** for **configuration, monitoring and management**

![[Pasted image 20240109163851.png]]
## Traditional Network Configuration

**Network devices such as router, switches and firewalls** configured using CLI

=> When change or new feature => **necessary configuration commands must be manually entered on all of the appropriate devices** => **time-consuming (prone to errors)

![[Pasted image 20240109165138.png]]

**Simple Network Management Protocol (SNMP)** developed to allow administrator to **manage nodes such as servers, workstations, routers, switches and security appliances** on an IP network

=> **SNMP** enables network administrators to **monitor and manage network performance** => to find problem and solved them

![[Pasted image 20240109170957.png]]

=> We can **use APIs** to automate the **deployment and management of network resources** 

=> Use tool to **automate configurations** such as configuring ports, access lists, quality of service (QoS) and load balancing policies

=> Reduce **repetitive and mundane tasks to free up time for network administrators to work on more important things**
## Network Automation

![[Pasted image 20240109171223.png]]
## Configuration Management Tools

Configuration management tools make **use of RESTful API requests to automate tasks and scale across thousands of devices** 

**Benefit from automating** :

- Software and version control
- Device attributes such as names, addressing and security
- Protocol configurations
- ACL configurations

**Several tools available to make configuration management easier** :

- Ansible
- Chef
- Puppet
- SaltStack

![[Pasted image 20240109171620.png]]

Goal of all these tools is **reduce the complexity and time involved in configuring and maintaining a large-scale network infrastructure with hundreds, even thousands of devices**
## Compare Ansible, Chef, Puppet and SaltStack

Ansible, Chef, Puppet and SaltStack come with API documentation for **configuring RESTful API requests** 

![[Pasted image 20240109171810.png]]
## Intent-Based Networking Overview

IBN is **emerging industry model for the next generation of networking** => IBN **builds on Software-Defined Networking (SDN)** transforming hardware-centric and manual approach to **designing and operating networks to one that is software-centric and fully automated**

=> Business objectives for the network expressed **as intend** 

**IBN capture business intent** and **uses analytics**, **machine learning and automation** to align the network **continuously and dynamically as business needs change**

=> IBN **capture and translates business intent into network policies** => can be **automated and applied consistently across the network** 

**IBN has three essential functions** : translation, activation and assurance => interact with physical and virtual infrastructure

![[Pasted image 20240110081302.png]]

- **Translation** : Function enables network administrator **express expected networking behavior that will best support the business intent** 

- **Activation** : Capture intent **needs to be interpreted into policies applied across the network** => activation function **installs policies into physical and virtual network infrastructure using networkwide automation** 

- **Assurance** : Continuously check the **expressed intent** is honored by network at any point in time => **assurance function maintains a continuous validation-and-verification loop**
## Network Infrastructure as Fabric

From perspective of **IBN** => **physical and virtual network infrastructure is a fabric** 

=> **Fabric is a term used to describe overlay that represents the logical topology to virtually connect to devices**

- Overlay **limits number of devices the administrator must program** => also **provides services and alternative forwarding methods not controlled by the underlying physical layer*

*Example : Overlay where encapsulation protocol like IP security (IPsec) and Control and Provisioning of Wireless Access Points (CAPWAP) occur* 

**Using IBN solution** => network administrator can **specify through policies** exactly **what happens in the overlay control plane** 
### Example Overlay Network

![[Pasted image 20240110081850.png]]

- **Underlay Network** : physical topology includes **all hardware require to meet business objectives** 

=> Underlay reveals **additional devices and specifies how these device are connected** 

=> End points such as **servers access the network through the Layer 2 devices** => underlay control plane is **responsible for simple forwarding tasks**
### Example Underlay Network

![[Pasted image 20240110082801.png]]
## Cisco Digital Network Architecture (DNA)

**Cisco implements IBN fabric** using **Cisco DNA** => business intent is **securely deployed into the network infrastructure (the fabric)** 

=> Cisco DNA **gathers data from multitude of sources** (devices and applications) to **provide a rich context of information** => **Information can be analyzed to make sure the network is performing securely** => at **optimal level** and **accordance with business intent and network policies

![[Pasted image 20240110083151.png]]

Cisco **DNA is a system constantly learning, adapting to support the business needs** 

=> List of some Cisco DNA products and solutions

![[Pasted image 20240110083325.png]]
## Cisco DNA Center

**Foundational controller and analytics platform at the heart of Cisco DNA** => **support expression of intent for multiple use cases** => including **basic automation capabilities, fabric provisioning and policy-based segmentation in the enterprise network

- Cisco DNA Center => **network management and command center** => **provisioning and configuring network devices** => **It's hardware and software platform providing a 'single-pane-of-glass'** (single-interface) that focuses **on assurance, analytics and automation**

![[Pasted image 20240110084414.png]]

- **Design** : **Model entire network** from sites and buildings to device and links both physical and virtual, across campus, branch and WAN and cloud

- **Policy** : Use **Policies to automate** and **simplify network management**, reducing cost and risk while speeding rollout of new and enhanced services

- **Provision** : New services to **users with ease, speed, and security across your enterprise network**, regardless of network size and complexity

- **Assurance** : Proactive monitoring and insight from the network, devices, application to predict problem faster and ensure **policy and configuration change achieve business intent** and the **user experience you want**

- **Platform** : Use **API** to **integrate your preferred IT systems to create end-to-end solutions** and **add support for multi-vendor devices** 