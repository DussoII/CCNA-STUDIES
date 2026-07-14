# VLAN Configuration

## Overview

A growing company currently operates all devices on a single broadcast domain, resulting in unnecessary network traffic and poor network segmentation. To improve security, performance, and network organization, the IT department has decided to separate each department into its own VLAN.

The objective of this lab is to configure VLANs on a Layer 2 switch, assign access ports to the appropriate VLANs, and verify that devices are correctly isolated.

---

## Objectives

- Create and name VLANs.
- Assign switch access ports to the appropriate VLAN.
- Verify VLAN membership.
- Validate Layer 2 segmentation.
- Analyze the switch MAC address table.

---

## VLAN Plan

| VLAN | Department |
|------|------------|
| 10 | Finance |
| 20 | Human Resources |
| 30 | IT |
| 40 | Sales |

---
## Addressing Plan

| Device | VLAN | IP Address   | Mask |
|--------|------|--------------|------|
| PC1    | 10   | 192.168.1.1  | /26  |
| PC2    | 10   | 192.168.1.2  | /26  |
| PC3    | 20   | 192.168.1.65 | /26  |
| PC4    | 20   | 192.168.1.66 | /26  |
| PC5    | 30   | 192.168.1.129| /26  |
| PC6    | 30   | 192.168.1.130| /26  |
| PC7    | 40   | 192.168.1.193| /26  |
| PC8    | 40   | 192.168.1.194| /26  |

---

## Tasks

- Create all required VLANs.
- Configure descriptive names for each VLAN.
- Assign the correct access ports to each department.
- Configure IP addresses for all hosts.
- Verify that devices within the same VLAN can communicate.
- Verify that communication between different VLANs is not possible.

---

## Validation

### Verify VLAN configuration

```bash
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
                                                Gig0/1, Gig0/2
10   Finance                          active    Fa0/1, Fa0/2
20   Human_Resources                  active    Fa0/3, Fa0/4
30   IT                               active    Fa0/5, Fa0/6
40   Sales                            active    Fa0/7, Fa0/8
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active 
```

### Verify learned MAC addresses

```bash
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

  10    0001.c79c.a2a0    DYNAMIC     Fa0/1
  10    0002.1647.8a34    DYNAMIC     Fa0/2
  20    0090.0ce0.bc03    DYNAMIC     Fa0/3
  20    00d0.d39c.6072    DYNAMIC     Fa0/4
  30    0001.96d6.8e4d    DYNAMIC     Fa0/6
  30    000b.be74.4e24    DYNAMIC     Fa0/5
  40    0001.428e.1a09    DYNAMIC     Fa0/7
  40    0001.c759.7392    DYNAMIC     Fa0/8
```

---