
# ==I. Single-Area OSPFv2 Concepts==

## OSPF (Open Shortest Path First)

- **Single-rea
- **Multiarea

- **OSPFv2 for IPv4 Networks
- **OSPFv3 for IPv6 Networks

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

SPF algorithm CPU-intensive and the time it takes for calculation depends on the size of the area 


**Hierarchical-Topology Design**

- **Smaller routing tables** : fewer routing table entries => network addresses summarized between areas => route summarization not enable by default

- **Reduced link-state update overhead** : Designing multiarea OSPF with smaller areas minimizes processing and memory 

- **Reduced frequency of SPF calculations** : Multiarea OSPF localize impact of a topology change within an area => minimizes routing update impact => LSA flooding stops at the area boundary

![[Pasted image 20231114101455.png]]

### OSPFv3

- Equivalent of OSPFv3 with exchanging IPv6 prefixes

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

NAT (Network Address Translation) developed

## IPv4 Private Address Space

- **Not enough public IPv4 addresses to assign a unique address to each device connected to the internet**

*Implemented private IPv4 addresses defined in RFC 1918*

![[Pasted image 20231209160719.png]]

- Used to **communicate locally within organization**
- **Private address must be translated to public address** => **NAT**

![[Pasted image 20231209161054.png]]

**Without NAT** => **exhaustion of IPv4 address space** would have occurred **before year 2000**
## What is NAT





































# ==VII. WAN Concepts==

4/12

-----------------------------------------------------
# ==VIII. VPN and IPsec Concepts==
# ==IX. QoS Concepts==
# ==X. Network Management==
# ==XI. Network Design==
# ==XII. Network Troubleshooting==
# ==XIII. Network Virtualization==
# ==XIV. Network Automation==


20/12