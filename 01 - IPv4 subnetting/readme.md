# VLSM Network Design and Static Routing Lab

## Scenario

A company has been assigned the **192.168.1.0/24** network and needs to divide it into subnets for four departments across two branch offices. Each department requires a different number of hosts:

| Network | Required Hosts |
|---------|---------------:|
| LAN 1 | 68 |
| LAN 2 | 41 |
| LAN 3 | 25 |
| LAN 4 | 5 |

Your tasks are to:

1. Plan the IP addressing scheme using **VLSM**.
2. Configure the network devices.
3. Configure static routing.
4. Verify end-to-end connectivity.

---

# Network Design

| Network | Prefix | Subnet Mask | Network Address | First Host | Last Host | Broadcast | Gateway |
|----------|:------:|-------------|-----------------|------------|-----------|-----------|----------|
| LAN 1 | /25 | 255.255.255.128 | 192.168.1.0 | 192.168.1.1 | 192.168.1.126 | 192.168.1.127 | 192.168.1.1 |
| LAN 2 | /26 | 255.255.255.192 | 192.168.1.128 | 192.168.1.129 | 192.168.1.190 | 192.168.1.191 | 192.168.1.129 |
| LAN 3 | /27 | 255.255.255.224 | 192.168.1.192 | 192.168.1.193 | 192.168.1.222 | 192.168.1.223 | 192.168.1.193 |
| LAN 4 | /29 | 255.255.255.248 | 192.168.1.224 | 192.168.1.225 | 192.168.1.230 | 192.168.1.231 | 192.168.1.225 |
| WAN | /30 | 255.255.255.252 | 192.168.1.232 | 192.168.1.233 | 192.168.1.234 | 192.168.1.235 | - |

---

# Configuration Steps

1. Calculated the required subnet sizes using **VLSM**.
2. Assigned subnet addresses and default gateways.
3. Configured IP addressing on router interfaces.
4. Configured static routes between routers.
5. Configured IP addresses and default gateways on all hosts.
6. Verified connectivity using:
   - `ping`
   - `show ip route`
   - `show ip interface brief`

---

# Router Configuration

## R1

```cisco
conf t

interface GigabitEthernet0/0
 ip address 192.168.1.234 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 ip address 192.168.1.1 255.255.255.128
 no shutdown

interface GigabitEthernet0/2
 ip address 192.168.1.129 255.255.255.192
 no shutdown

ip route 192.168.1.192 255.255.255.224 192.168.1.233
ip route 192.168.1.224 255.255.255.248 192.168.1.233
```

## R2

```cisco
conf t

interface GigabitEthernet0/0
 ip address 192.168.1.233 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 ip address 192.168.1.193 255.255.255.224
 no shutdown

interface GigabitEthernet0/2
 ip address 192.168.1.225 255.255.255.248
 no shutdown

ip route 192.168.1.0 255.255.255.128 192.168.1.234
ip route 192.168.1.128 255.255.255.192 192.168.1.234
```

---

# Host Configuration

| Device | IP Address | Subnet Mask | Default Gateway |
|---------|------------|-------------|-----------------|
| PC1 | 192.168.1.2 | 255.255.255.128 | 192.168.1.1 |
| PC2 | 192.168.1.130 | 255.255.255.192 | 192.168.1.129 |
| PC3 | 192.168.1.194 | 255.255.255.224 | 192.168.1.193 |
| PC4 | 192.168.1.226 | 255.255.255.248 | 192.168.1.225 |

---

# Validation

## Commands

```cisco
show ip route
show ip interface brief
ping <destination>
```

## R1 Routing Table

```text
<R1#sho ip route 
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     192.168.1.0/24 is variably subnetted, 8 subnets, 6 masks
C       192.168.1.0/25 is directly connected, GigabitEthernet0/1
L       192.168.1.1/32 is directly connected, GigabitEthernet0/1
C       192.168.1.128/26 is directly connected, GigabitEthernet0/2
L       192.168.1.129/32 is directly connected, GigabitEthernet0/2
S       192.168.1.192/27 [1/0] via 192.168.1.233
S       192.168.1.224/29 [1/0] via 192.168.1.233
C       192.168.1.232/30 is directly connected, GigabitEthernet0/0
L       192.168.1.234/32 is directly connected, GigabitEthernet0/0

R1#sh ip inter brie
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     192.168.1.234   YES manual up                    up 
GigabitEthernet0/1     192.168.1.1     YES manual up                    up 
GigabitEthernet0/2     192.168.1.129   YES manual up                    up 
Vlan1                  unassigned      YES unset  administratively down down
R1#ping 192.168.1.194

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.194, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 0/0/0 ms>
```

## R2 Routing Table

```text
<R2#sh ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     192.168.1.0/24 is variably subnetted, 8 subnets, 6 masks
S       192.168.1.0/25 [1/0] via 192.168.1.234
S       192.168.1.128/26 [1/0] via 192.168.1.234
C       192.168.1.192/27 is directly connected, GigabitEthernet0/1
L       192.168.1.193/32 is directly connected, GigabitEthernet0/1
C       192.168.1.224/29 is directly connected, GigabitEthernet0/2
L       192.168.1.225/32 is directly connected, GigabitEthernet0/2
C       192.168.1.232/30 is directly connected, GigabitEthernet0/0
L       192.168.1.233/32 is directly connected, GigabitEthernet0/0

R2#sh ip int brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     192.168.1.233   YES manual up                    up 
GigabitEthernet0/1     192.168.1.193   YES manual up                    up 
GigabitEthernet0/2     192.168.1.225   YES manual up                    up 
Vlan1                  unassigned      YES unset  administratively down down
R2#ping 192.168.1.130

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.130, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 0/0/0 ms>
```

---

# Result

All hosts were able to communicate successfully across both branch offices using static routing. The routing tables confirmed the correct installation of connected and static routes, and end-to-end connectivity was successfully verified.