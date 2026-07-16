w# Inter-VLAN Routing using Router-on-a-Stick (ROAS)

## Scenario

The company has successfully deployed VLANs and IEEE 802.1Q trunking between both access switches. While devices within the same VLAN can communicate across the network, communication between different VLANs is still unavailable because each VLAN is an independent Layer 2 broadcast domain.

To enable communication between departments without redesigning the existing infrastructure, the IT department decided to implement **Router-on-a-Stick (ROAS)**. A router will be connected to the core switch using an IEEE 802.1Q trunk, allowing a single physical interface to route traffic between multiple VLANs.

The objective of this lab is to implement inter-VLAN routing while preserving the existing VLAN and trunk topology.

---

# Network Design

## VLAN Plan

| VLAN | Department |
| :--: | ---------- |
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

### SW1

| Interface | VLAN | Device |
|-----------|:----:|--------|
| Fa0/1 | 10 | PC1 |
| Fa0/2 | 20 | PC2 |
| Fa0/3 | 30 | PC3 |
| Fa0/4 | 40 | PC4 |
| Gi0/1 | Trunk | SW2 |
| Gi0/2 | Trunk | R1 |

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

- Connect the router to SW1.
- Configure the switch interface connected to the router as an IEEE 802.1Q trunk.
- Create one router subinterface for each VLAN.
- Configure IEEE 802.1Q encapsulation.
- Assign the default gateway IP address to each subinterface.
- Configure the hosts with the appropriate default gateway.
- Validate inter-VLAN communication.

---

# Device Configuration

## SW1

```text
vlan 10
 name Finance

vlan 20
 name HR

vlan 30
 name IT

vlan 40
 name Sales

interface fa0/1
 switchport mode access
 switchport access vlan 10

interface fa0/2
 switchport mode access
 switchport access vlan 20

interface fa0/3
 switchport mode access
 switchport access vlan 30

interface fa0/4
 switchport mode access
 switchport access vlan 40

interface gi0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40

interface gi0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40
```

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

interface fa0/1
 switchport mode access
 switchport access vlan 10

interface fa0/2
 switchport mode access
 switchport access vlan 20

interface fa0/3
 switchport mode access
 switchport access vlan 30

interface fa0/4
 switchport mode access
 switchport access vlan 40

interface gi0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40
```

## R1

```text
interface g0/0
 no shutdown

interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.1.62 255.255.255.192

interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.1.126 255.255.255.192

interface g0/0.30
 encapsulation dot1Q 30
 ip address 192.168.1.190 255.255.255.192

interface g0/0.40
 encapsulation dot1Q 40
 ip address 192.168.1.254 255.255.255.192
```

---

# Validation

## Commands

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ip route
```

## Expected Results

### SW1

- VLANs 10, 20, 30 and 40 configured.
- Gi0/1 trunking with SW2.
- Gi0/2 trunking with R1.

### SW2

- VLANs 10, 20, 30 and 40 configured.
- Gi0/1 trunking with SW1.

### R1

```text
Interface              IP-Address
G0/0                   unassigned
G0/0.10                192.168.1.62
G0/0.20                192.168.1.126
G0/0.30                192.168.1.190
G0/0.40                192.168.1.254
```

---

## Result

Router-on-a-Stick was successfully implemented by using IEEE 802.1Q subinterfaces on a single router interface. Inter-VLAN communication is now available while preserving the existing Layer 2 VLAN and trunk infrastructure.
