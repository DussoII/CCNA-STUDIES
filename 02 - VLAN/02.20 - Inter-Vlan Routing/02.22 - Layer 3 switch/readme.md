# Inter-VLAN Routing using a Layer 3 Switch (SVIs)

## Scenario

The company has successfully deployed VLANs and IEEE 802.1Q trunking between both access switches. While devices within the same VLAN can communicate across the network, communication between different VLANs requires Layer 3 routing.
Instead of using **Router-on-a-Stick (ROAS)**, the network has been upgraded to a **Layer 3 switch**. The switch performs routing internally through **Switched Virtual Interfaces (SVIs)**, eliminating the need for router subinterfaces and improving network performance.
The **Layer 3 switch is responsible for all inter-VLAN routing** and serves as the default gateway for every VLAN. A dedicated routed point-to-point link connects the switch to the router, which acts as the next-hop gateway for external networks.
The objective of this lab is to configure inter-VLAN routing using a Layer 3 switch while preserving the existing VLAN and trunk topology.

---

# Network Design

## VLAN Plan

| VLAN | Department |
|:---:|------------|
| 10 | Finance |
| 20 | Human Resources |
| 30 | IT |
| 40 | Sales |

---

## Addressing Plan

| Network | Prefix | Network Address | Gateway | Broadcast |
|:------:|:------:|-----------------|----------|-----------|
| VLAN 10 | /26 | 192.168.1.0 | 192.168.1.62 | 192.168.1.63 |
| VLAN 20 | /26 | 192.168.1.64 | 192.168.1.126 | 192.168.1.127 |
| VLAN 30 | /26 | 192.168.1.128 | 192.168.1.190 | 192.168.1.191 |
| VLAN 40 | /26 | 192.168.1.192 | 192.168.1.254 | 192.168.1.255 |
| Routed Link | /26 | 192.168.2.0 | SW1: 192.168.2.2 / R1: 192.168.2.1 | 192.168.2.63 |

---

## Host Configuration

| Device | VLAN | IP Address | Prefix | Default Gateway |
|---------|:----:|------------|:------:|-----------------|
| PC1 | 10 | 192.168.1.1 | /26 | 192.168.1.62 |
| PC5 | 10 | 192.168.1.2 | /26 | 192.168.1.62 |
| PC2 | 20 | 192.168.1.65 | /26 | 192.168.1.126 |
| PC6 | 20 | 192.168.1.66 | /26 | 192.168.1.126 |
| PC3 | 30 | 192.168.1.129 | /26 | 192.168.1.190 |
| PC7 | 30 | 192.168.1.130 | /26 | 192.168.1.190 |
| PC4 | 40 | 192.168.1.193 | /26 | 192.168.1.254 |
| PC8 | 40 | 192.168.1.194 | /26 | 192.168.1.254 |

---

## Switch Port Assignment

### SW1 (Layer 3 Switch)

| Interface | Mode | Device |
|-----------|------|--------|
| Fa0/1 | Access VLAN 10 | PC1 |
| Fa0/2 | Access VLAN 20 | PC2 |
| Fa0/3 | Access VLAN 30 | PC3 |
| Fa0/4 | Access VLAN 40 | PC4 |
| Gi0/1 | Trunk | SW2 |
| Gi0/2 | Routed Port | R1 |

### SW2

| Interface | VLAN | Device |
|-----------|:----:|--------|
| Fa0/1 | 10 | PC5 |
| Fa0/2 | 20 | PC6 |
| Fa0/3 | 30 | PC7 |
| Fa0/4 | 40 | PC8 |
| Gi0/1 | Trunk | SW1 |

---

# Tasks

- Configure VLANs on both switches.
- Configure access ports.
- Configure the IEEE 802.1Q trunk between SW1 and SW2.
- Enable Layer 3 routing on SW1.
- Create one Switched Virtual Interface (SVI) for each VLAN.
- Configure the default gateway IP address for every VLAN.
- Convert the interface connected to the router into a routed port.
- Configure a default route on the Layer 3 switch.
- Configure a static route on the router back to the VLAN networks.
- Verify inter-VLAN connectivity.

---

# Device Configuration

## SW1 (Layer 3 Switch)

```text
ip routing

vlan 10
 name Finance

vlan 20
 name HR

vlan 30
 name IT

vlan 40
 name Sales

interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10

interface FastEthernet0/2
 switchport mode access
 switchport access vlan 20

interface FastEthernet0/3
 switchport mode access
 switchport access vlan 30

interface FastEthernet0/4
 switchport mode access
 switchport access vlan 40

interface GigabitEthernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40

interface GigabitEthernet0/2
 no switchport
 ip address 192.168.2.2 255.255.255.192
 no shutdown

interface Vlan10
 ip address 192.168.1.62 255.255.255.192
 no shutdown

interface Vlan20
 ip address 192.168.1.126 255.255.255.192
 no shutdown

interface Vlan30
 ip address 192.168.1.190 255.255.255.192
 no shutdown

interface Vlan40
 ip address 192.168.1.254 255.255.255.192
 no shutdown

ip route 0.0.0.0 0.0.0.0 192.168.2.1
```

---

## SW2

```text
vlan 10
 name Finance

vlan 20
 name HR

vlan 30
 name IT

vlan 40
 name Sales

interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10

interface FastEthernet0/2
 switchport mode access
 switchport access vlan 20

interface FastEthernet0/3
 switchport mode access
 switchport access vlan 30

interface FastEthernet0/4
 switchport mode access
 switchport access vlan 40

interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40
```

---

## R1

```text
interface GigabitEthernet0/0
 ip address 192.168.2.1 255.255.255.192
 no shutdown

ip route 192.168.1.0 255.255.255.0 192.168.2.2
```

---

# Validation

## Commands

### SW1

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
```

### SW2

```text
show vlan brief
show interfaces trunk
```

### R1

```text
show ip interface brief
show ip route
```

---

## Expected Results

### SW1

- VLANs 10, 20, 30 and 40 configured.
- IEEE 802.1Q trunk established with SW2.
- Layer 3 routing enabled.
- Four active SVIs acting as the default gateways.
- Routed interface connected to R1.
- Default route pointing to **192.168.2.1**.

```text
Interface              IP-Address

GigabitEthernet0/2     192.168.2.2
Vlan10                 192.168.1.62
Vlan20                 192.168.1.126
Vlan30                 192.168.1.190
Vlan40                 192.168.1.254
```

### SW2

- VLANs 10, 20, 30 and 40 configured.
- Gi0/1 operating as an IEEE 802.1Q trunk.

### R1

```text
Interface              IP-Address

GigabitEthernet0/0     192.168.2.1
```

---

## Result

Inter-VLAN routing was successfully implemented using a **Layer 3 switch**. Instead of relying on router subinterfaces, the switch performs routing internally through **Switched Virtual Interfaces (SVIs)**, providing one default gateway for each VLAN.
The Layer 3 switch is responsible for all inter-VLAN routing, while the router acts only as the next-hop device for external networks. A dedicated routed link connects both devices, and a static route on the router provides reachability back to all VLAN networks.
Compared to the Router-on-a-Stick design, this implementation reduces latency, increases throughput, removes the bottleneck of a single trunk connected to the router, and provides a more scalable architecture commonly found in enterprise campus networks.
```
