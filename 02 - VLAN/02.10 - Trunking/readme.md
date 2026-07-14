# VLAN Trunking Lab

## Scenario

The company has expanded its office and installed a second access switch to support additional workstations. Since employees from every department are now distributed across both switches, a single VLAN per switch is no longer sufficient.

To avoid deploying separate physical links for each VLAN, the IT department decided to use an IEEE 802.1Q trunk link between the switches.

The objective of this lab is to configure a trunk connection capable of carrying traffic from multiple VLANs while preserving network segmentation across both switches.

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

| Device | VLAN | IP Address | Prefix |
|---------|:----:|------------|:------:|
| PC1 | 10 | 192.168.1.1 | /26 |
| PC5 | 10 | 192.168.1.2 | /26 |
| PC2 | 20 | 192.168.1.65 | /26 |
| PC6 | 20 | 192.168.1.66 | /26 |
| PC3 | 30 | 192.168.1.129 | /26 |
| PC7 | 30 | 192.168.1.130 | /26 |
| PC4 | 40 | 192.168.1.193 | /26 |
| PC8 | 40 | 192.168.1.194 | /26 |

---

# Switch Port Assignment

### SW1

| Interface | VLAN | Device |
|-----------|:----:|--------|
| Fa0/1 | 10 | PC1 |
| Fa0/2 | 20 | PC2 |
| Fa0/3 | 30 | PC3 |
| Fa0/4 | 40 | PC4 |
| Gi0/1 | Trunk | SW2 |

### SW2

| Interface | VLAN | Device |
|-----------|:----:|--------|
| Fa0/1 | 10 | PC5 |
| Fa0/2 | 20 | PC6 |
| Fa0/3 | 30 | PC7 |
| Fa0/4 | 40 | PC8 |
| Gi0/1 | Trunk | SW1 |

---

## Configuration Steps

1. Create the required VLANs on both switches.
2. Assign descriptive names to each VLAN.
3. Configure access ports for each department.
4. Configure the inter-switch link as an IEEE 802.1Q trunk.
5. Allow VLANs 10, 20, 30 and 40 across the trunk.
6. Configure IP addresses on all hosts.
7. Validate VLAN membership and end-to-end connectivity.

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

---

# Host Configuration

| Device | IP Address | Subnet Mask |
|---------|------------|-------------|
| PC1 | 192.168.1.1 | 255.255.255.192 |
| PC5 | 192.168.1.2 | 255.255.255.192 |
| PC2 | 192.168.1.65 | 255.255.255.192 |
| PC6 | 192.168.1.66 | 255.255.255.192 |
| PC3 | 192.168.1.129 | 255.255.255.192 |
| PC7 | 192.168.1.130 | 255.255.255.192 |
| PC4 | 192.168.1.193 | 255.255.255.192 |
| PC8 | 192.168.1.194 | 255.255.255.192 |

---

> **Note:** No default gateway is configured because this lab focuses exclusively on Layer 2 switching. Inter-VLAN routing will be covered in the next lab.

# Validation

## Commands

```text
show vlan brief
show interfaces trunk
```

## SW1 

```text
SW1#sh vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/2
10   Finance                          active    Fa0/1
20   HR                               active    Fa0/2
30   IT                               active    Fa0/3
40   sales                            active    Fa0/4
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active 

SW1#show interfaces trunk
Port        Mode         Encapsulation  Status        Native vlan
Gig0/1      on           802.1q         trunking      1

Port        Vlans allowed on trunk
Gig0/1      10,20,30,40

Port        Vlans allowed and active in management domain
Gig0/1      10,20,30,40

Port        Vlans in spanning tree forwarding state and not pruned
Gig0/1      10,20,30,40
```

## SW2

```text
SW2#sh vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/2
10   Finance                          active    Fa0/1
20   HR                               active    Fa0/2
30   IT                               active    Fa0/3
40   Sales                            active    Fa0/4
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active 

SW2#show interfaces trunk
Port        Mode         Encapsulation  Status        Native vlan
Gig0/1      on           802.1q         trunking      1

Port        Vlans allowed on trunk
Gig0/1      10,20,30,40

Port        Vlans allowed and active in management domain
Gig0/1      10,20,30,40

Port        Vlans in spanning tree forwarding state and not pruned
Gig0/1      10,20,30,40
```

## Result

The trunk link successfully transported VLANs 10, 20, 30 and 40 between both switches. Hosts belonging to the same VLAN communicated normally across the trunk, while communication between different VLANs remained blocked because no Layer 3 device was configured.
