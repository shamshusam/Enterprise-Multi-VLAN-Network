````markdown
# Router Configuration

## 1. Overview

This project uses two Cisco 1941 routers:

- Router0
- Router1

Router1 is used for:

- Inter-VLAN routing
- DHCP
- Server network connectivity
- Router-to-router communication
- Static routing

Router0 is used for:

- Router-to-router communication
- Static routing
- Loopback network testing

---

## 2. Router Topology

```text
                         Enterprise VLAN Network
                                  |
                                  |
                              Router1
                            /         \
                           /           \
                    VLAN Subinterfaces  \
                                         \
                                      G0/1
                                         |
                                         |
                                      G0/1
                                      Router0
                                         |
                                      Loopback0
                                    10.10.10.1
````

The router-to-router connection uses a `/30` network:

```text
Router0 G0/1 → 10.0.0.1
Router1 G0/1 → 10.0.0.2
```

---

# 3. Router1 Configuration

Router1 performs Router-on-a-Stick configuration for the five VLANs.

The physical interface is:

```text
GigabitEthernet0/0
```

The VLAN subinterfaces are:

```text
G0/0.10 → VLAN 10
G0/0.20 → VLAN 20
G0/0.30 → VLAN 30
G0/0.40 → VLAN 40
G0/0.50 → VLAN 50
```

---

# 4. Enable Router1 Physical Interface

Configuration:

```text
enable
configure terminal

interface gigabitEthernet0/0
no shutdown
exit
```

The physical interface must be enabled before the subinterfaces can communicate with the switch.

---

# 5. VLAN 10 Subinterface

VLAN 10 is used for the HR department.

```text
interface gigabitEthernet0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit
```

Gateway:

```text
192.168.10.1
```

Network:

```text
192.168.10.0/24
```

---

# 6. VLAN 20 Subinterface

VLAN 20 is used for the IT department.

```text
interface gigabitEthernet0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit
```

Gateway:

```text
192.168.20.1
```

Network:

```text
192.168.20.0/24
```

---

# 7. VLAN 30 Subinterface

VLAN 30 is used for the Finance department.

```text
interface gigabitEthernet0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
exit
```

Gateway:

```text
192.168.30.1
```

Network:

```text
192.168.30.0/24
```

---

# 8. VLAN 40 Subinterface

VLAN 40 is used for Management.

```text
interface gigabitEthernet0/0.40
encapsulation dot1Q 40
ip address 192.168.40.1 255.255.255.0
exit
```

Gateway:

```text
192.168.40.1
```

Network:

```text
192.168.40.0/24
```

---

# 9. VLAN 50 Subinterface

VLAN 50 is used for the server network.

```text
interface gigabitEthernet0/0.50
encapsulation dot1Q 50
ip address 192.168.50.1 255.255.255.0
exit
```

Gateway:

```text
192.168.50.1
```

Network:

```text
192.168.50.0/24
```

---

# 10. Complete Router1 VLAN Configuration

The complete Router1 Router-on-a-Stick configuration is:

```text
enable
configure terminal

interface gigabitEthernet0/0
no shutdown
exit

interface gigabitEthernet0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit

interface gigabitEthernet0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit

interface gigabitEthernet0/0.30
encapsulation dot1Q 30
ip address 192.168.30.1 255.255.255.0
exit

interface gigabitEthernet0/0.40
encapsulation dot1Q 40
ip address 192.168.40.1 255.255.255.0
exit

interface gigabitEthernet0/0.50
encapsulation dot1Q 50
ip address 192.168.50.1 255.255.255.0
exit

end
write memory
```

---

# 11. What Is Router-on-a-Stick?

Router-on-a-Stick is a method of using one physical router interface with multiple subinterfaces to route traffic between VLANs.

In this project:

```text
Router1 G0/0
      |
      +---- G0/0.10 → VLAN 10
      |
      +---- G0/0.20 → VLAN 20
      |
      +---- G0/0.30 → VLAN 30
      |
      +---- G0/0.40 → VLAN 40
      |
      +---- G0/0.50 → VLAN 50
```

Each subinterface acts as the gateway for its VLAN.

---

# 12. What Does `encapsulation dot1Q` Mean?

The command:

```text
encapsulation dot1Q 10
```

associates the subinterface with VLAN 10.

For example:

```text
G0/0.10
encapsulation dot1Q 10
```

means that the subinterface handles traffic belonging to VLAN 10.

Similarly:

```text
G0/0.20
encapsulation dot1Q 20
```

handles VLAN 20 traffic.

---

# 13. Router-to-Router Connection

Router0 and Router1 are connected using:

```text
Router0 G0/1
      |
      |
Router1 G0/1
```

The connection uses a `/30` network.

Network:

```text
10.0.0.0/30
```

Usable IP addresses:

```text
10.0.0.1
10.0.0.2
```

---

# 14. Router0 G0/1 Configuration

Router0 uses:

```text
10.0.0.1
```

Configuration:

```text
enable
configure terminal

interface gigabitEthernet0/1
ip address 10.0.0.1 255.255.255.252
no shutdown

end
write memory
```

---

# 15. Router1 G0/1 Configuration

Router1 uses:

```text
10.0.0.2
```

Configuration:

```text
enable
configure terminal

interface gigabitEthernet0/1
ip address 10.0.0.2 255.255.255.252
no shutdown

end
write memory
```

---

# 16. Why Did We Use `/30`?

The router-to-router connection only needs two usable IP addresses.

A `/30` subnet provides:

```text
Network address:   10.0.0.0
Usable address:    10.0.0.1
Usable address:    10.0.0.2
Broadcast address: 10.0.0.3
```

Therefore:

```text
Router0 → 10.0.0.1
Router1 → 10.0.0.2
```

---

# 17. Testing the Router-to-Router Connection

From Router0:

```text
ping 10.0.0.2
```

From Router1:

```text
ping 10.0.0.1
```

Successful replies confirm that the router-to-router link is working.

---

# 18. Router0 Loopback Interface

A loopback interface was created on Router0 for remote network testing.

Configuration:

```text
enable
configure terminal

interface loopback0
ip address 10.10.10.1 255.255.255.0

end
write memory
```

Loopback address:

```text
10.10.10.1
```

Network:

```text
10.10.10.0/24
```

---

# 19. Why Did We Use a Loopback?

A loopback interface is a logical interface that does not depend on a physical cable.

In this project, it provides a remote network that can be reached through Router0.

It is useful for testing routing.

For example:

```text
PC
 |
Router1
 |
10.0.0.0/30
 |
Router0
 |
Loopback0
10.10.10.1
```

---

# 20. Static Routing

Static routes were configured so that the routers know how to reach remote networks.

Router1 needs a route to Router0's loopback network.

Router0 needs a route toward the networks behind Router1.

---

# 21. Static Route on Router1

Router1 uses:

```text
10.0.0.1
```

as the next hop toward Router0.

Configuration:

```text
enable
configure terminal

ip route 10.10.10.0 255.255.255.0 10.0.0.1

end
write memory
```

This tells Router1:

```text
To reach 10.10.10.0/24,
send the traffic to 10.0.0.1.
```

---

# 22. Default Route on Router0

Router0 uses Router1 as its next hop.

Configuration:

```text
enable
configure terminal

ip route 0.0.0.0 0.0.0.0 10.0.0.2

end
write memory
```

This is a default route.

It means traffic for destinations that are not otherwise known is sent to:

```text
10.0.0.2
```

---

# 23. Verify Router Interfaces

Use:

```text
show ip interface brief
```

This command displays:

* Interface name
* IP address
* Status
* Protocol

Expected important interfaces include:

```text
G0/0
G0/0.10
G0/0.20
G0/0.30
G0/0.40
G0/0.50
G0/1
```

---

# 24. Verify Routing Table

Use:

```text
show ip route
```

The routing table should show connected networks and configured static routes.

For example, Router1 should contain:

```text
10.0.0.0/30
10.10.10.0/24
192.168.10.0/24
192.168.20.0/24
192.168.30.0/24
192.168.40.0/24
192.168.50.0/24
```

---

# 25. Test Inter-VLAN Routing

A PC in VLAN 10 can test another VLAN by pinging its gateway.

For example:

```text
ping 192.168.20.1
```

If the ping succeeds, Router1 is routing traffic between VLAN 10 and VLAN 20.

Another example:

```text
ping 192.168.50.10
```

This tests communication from a PC to the server network.

---

# 26. End-to-End Routing Test

The network can be tested from a PC all the way to Router0's loopback.

For example:

```text
ping 10.10.10.1
```

The traffic path is:

```text
PC
 ↓
Access Switch
 ↓
Core Switch
 ↓
Router1
 ↓
10.0.0.0/30
 ↓
Router0
 ↓
Loopback0
10.10.10.1
```

A successful ping confirms that VLANs, trunking, inter-VLAN routing, router-to-router connectivity, and static routing are working together.

---

# 27. Router Configuration Verification Commands

Important commands:

```text
show ip interface brief
show ip route
show running-config
show interfaces
ping <IP-address>
```

These commands are used to verify and troubleshoot router configuration.

---

# 28. Common Router Problems

## Problem 1: Interface Is Down

Check:

```text
show ip interface brief
```

If an interface is administratively down, enable it:

```text
configure terminal
interface gigabitEthernet0/1
no shutdown
```

---

## Problem 2: VLAN Gateway Does Not Respond

Check:

```text
show ip interface brief
```

Make sure the VLAN subinterface is up.

Also check:

```text
show interfaces trunk
```

The switch-to-router link must be configured as a trunk.

---

## Problem 3: Remote Network Cannot Be Reached

Check:

```text
show ip route
```

Make sure the required static route exists.

For example:

```text
ip route 10.10.10.0 255.255.255.0 10.0.0.1
```

---

## Problem 4: Router-to-Router Ping Fails

Check:

```text
show ip interface brief
```

Verify:

```text
Router0 G0/1 → 10.0.0.1
Router1 G0/1 → 10.0.0.2
```

Then test:

```text
ping 10.0.0.2
```

from Router0.

---

# 29. Router Configuration Summary

```text
Router0
G0/1       → 10.0.0.1/30
Loopback0  → 10.10.10.1/24

Router1
G0/0.10    → 192.168.10.1/24
G0/0.20    → 192.168.20.1/24
G0/0.30    → 192.168.30.1/24
G0/0.40    → 192.168.40.1/24
G0/0.50    → 192.168.50.1/24
G0/1       → 10.0.0.2/30
```

---

# 30. What I Learned

Through the router configuration, I learned:

* How router interfaces are configured
* How subinterfaces work
* What Router-on-a-Stick means
* How 802.1Q VLAN tagging is used
* How routers communicate using IP addresses
* Why a `/30` subnet is useful for point-to-point links
* How to configure static routes
* How to configure a loopback interface
* How to verify routing tables
* How to troubleshoot router connectivity

---

## Conclusion

Router1 provides the default gateway for the VLANs and performs inter-VLAN routing using Router-on-a-Stick.

Router0 and Router1 communicate through a `/30` point-to-point network.

Static routing allows Router1 to reach Router0's loopback network, while Router0 uses a default route toward Router1.

Together, these configurations allow the enterprise network to communicate across VLANs and between routers.

```
