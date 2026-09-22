````markdown
# Network Verification

## 1. What is Network Verification?

Network verification means checking whether all devices, interfaces, VLANs, routing, DHCP services, and connections are working correctly.

In this project, verification is used to confirm that:

- VLANs are configured correctly
- Access ports belong to the correct VLAN
- Trunk links are working
- Router-on-a-Stick is working
- DHCP is assigning IP addresses
- Router-to-router communication is working
- Static routing is working
- Server connectivity is working
- Devices in different VLANs can communicate
- End-to-end communication is successful

---

# 2. Verification Process

The network can be verified in the following order:

```text
Switches
   ↓
VLANs
   ↓
Trunks
   ↓
Router Interfaces
   ↓
DHCP
   ↓
Routing
   ↓
Server
   ↓
End-to-End Ping
````

---

# 3. Verify VLANs

On each switch, use:

```text
show vlan brief
```

This command displays:

* VLAN ID
* VLAN name
* VLAN status
* Access ports assigned to each VLAN

Expected VLANs:

| VLAN | Name       | Network         |
| ---- | ---------- | --------------- |
| 10   | HR         | 192.168.10.0/24 |
| 20   | IT         | 192.168.20.0/24 |
| 30   | FINANCE    | 192.168.30.0/24 |
| 40   | MANAGEMENT | 192.168.40.0/24 |
| 50   | SERVERS    | 192.168.50.0/24 |

---

# 4. Verify Access Ports

Access ports connect end devices such as PCs and servers to the correct VLAN.

Use:

```text
show vlan brief
```

Example:

```text
SW1 Fa0/1 → VLAN 10
SW1 Fa0/2 → VLAN 20
SW1 Fa0/3 → VLAN 30
```

Other switch assignments:

```text
SW2 Fa0/1 → VLAN 10
SW2 Fa0/2 → VLAN 20
SW2 Fa0/3 → VLAN 40

SW3 Fa0/1 → VLAN 10
SW3 Fa0/2 → VLAN 30
SW3 Fa0/3 → VLAN 40

Core-SW Fa0/5 → VLAN 20
Core-SW Fa0/6 → VLAN 30
Core-SW Fa0/7 → VLAN 40
Core-SW Fa0/8 → VLAN 50
```

---

# 5. Verify Trunk Links

Trunk links carry traffic for multiple VLANs between switches and between the switch and router.

Use:

```text
show interfaces trunk
```

Expected trunk connections:

```text
SW1 Fa0/24 ↔ Core-SW Fa0/2
SW2 Fa0/24 ↔ Core-SW Fa0/3
SW3 Fa0/24 ↔ Core-SW Fa0/4
Core-SW Fa0/1 ↔ Router1 G0/0
```

Allowed VLANs:

```text
10,20,30,40,50
```

---

# 6. Verify Switch Interfaces

Use:

```text
show interfaces status
```

This command helps verify:

* Port status
* VLAN assignment
* Speed
* Duplex
* Connected interfaces

Ports used by the project should normally show:

```text
connected
```

---

# 7. Verify Router Interfaces

On Router1:

```text
show ip interface brief
```

Expected Router1 interfaces:

| Interface | IP Address   | Status |
| --------- | ------------ | ------ |
| G0/0      | -            | up/up  |
| G0/0.10   | 192.168.10.1 | up/up  |
| G0/0.20   | 192.168.20.1 | up/up  |
| G0/0.30   | 192.168.30.1 | up/up  |
| G0/0.40   | 192.168.40.1 | up/up  |
| G0/0.50   | 192.168.50.1 | up/up  |
| G0/1      | 10.0.0.2     | up/up  |

Router0:

```text
show ip interface brief
```

Expected:

| Interface | IP Address |
| --------- | ---------- |
| G0/1      | 10.0.0.1   |
| Loopback0 | 10.10.10.1 |

---

# 8. Verify DHCP

On Router1:

```text
show ip dhcp binding
```

This displays IP addresses assigned to PCs.

Example:

```text
192.168.10.11
192.168.20.11
192.168.30.11
192.168.40.11
```

To view DHCP pools:

```text
show ip dhcp pool
```

The pools used in this project are:

```text
HR
IT
FINANCE
MANAGEMENT
```

---

# 9. Verify PC IP Address

On a PC, open:

```text
Desktop → Command Prompt
```

Then enter:

```text
ipconfig
```

A DHCP-configured PC should have:

* An IP address from its VLAN network
* Subnet mask `255.255.255.0`
* Correct default gateway
* DNS server `8.8.8.8`

Example for an HR PC:

```text
IP Address:      192.168.10.11
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.10.1
DNS Server:      8.8.8.8
```

---

# 10. Verify Local VLAN Gateway

From PC0:

```text
ping 192.168.10.1
```

Expected result:

```text
Reply from 192.168.10.1
```

This verifies communication between PC0 and the HR VLAN gateway.

---

# 11. Verify Inter-VLAN Routing

From PC0:

```text
ping 192.168.20.1
```

Expected result:

```text
Reply from 192.168.20.1
```

This verifies that traffic can move from VLAN 10 toward VLAN 20 through Router1.

Another test:

```text
ping 192.168.50.10
```

This tests communication from VLAN 10 to the Server VLAN.

---

# 12. Verify Router-to-Router Link

On Router0:

```text
ping 10.0.0.2
```

Expected result:

```text
!!!!!
```

On Router1:

```text
ping 10.0.0.1
```

Expected result:

```text
!!!!!
```

The `/30` network used between the routers is:

```text
10.0.0.0/30
```

Router0:

```text
10.0.0.1
```

Router1:

```text
10.0.0.2
```

---

# 13. Verify Static Routing

On Router1:

```text
show ip route
```

Router1 should contain a static route to:

```text
10.10.10.0/24
```

through:

```text
10.0.0.1
```

Expected route:

```text
S 10.10.10.0/24 [1/0] via 10.0.0.1
```

`S` means the route was manually configured as a static route.

---

# 14. Verify Router0 Default Route

On Router0:

```text
show ip route
```

Router0 should contain a default route:

```text
0.0.0.0/0
```

through:

```text
10.0.0.2
```

The configured command is:

```text
ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

---

# 15. Verify Loopback Connectivity

Router0 has:

```text
Loopback0
IP Address: 10.10.10.1
```

From PC0:

```text
ping 10.10.10.1
```

Expected result:

```text
Reply from 10.10.10.1
```

This verifies end-to-end communication:

```text
PC0
 ↓
SW1
 ↓
Core-SW
 ↓
Router1
 ↓
Router0
 ↓
Loopback0
```

---

# 16. Verify Server Connectivity

Server configuration:

```text
IP Address:      192.168.50.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.50.1
```

From PC0:

```text
ping 192.168.50.10
```

Expected result:

```text
Reply from 192.168.50.10
```

This verifies communication between the HR VLAN and Server VLAN.

---

# 17. End-to-End Verification

The following tests were performed successfully.

| Source  | Destination   | Purpose                    | Result     |
| ------- | ------------- | -------------------------- | ---------- |
| PC0     | 192.168.10.1  | Test VLAN 10 gateway       | Successful |
| PC0     | 192.168.20.1  | Test inter-VLAN routing    | Successful |
| PC0     | 192.168.50.10 | Test server connectivity   | Successful |
| PC0     | 10.10.10.1    | Test end-to-end routing    | Successful |
| Router0 | 10.0.0.2      | Test router-to-router link | Successful |
| Router1 | 10.0.0.1      | Test router-to-router link | Successful |

---

# 18. Useful Verification Commands

## Switch Commands

```text
show vlan brief
show interfaces trunk
show interfaces status
show running-config
```

## Router Commands

```text
show ip interface brief
show ip route
show ip dhcp binding
show ip dhcp pool
show running-config
```

## PC Commands

```text
ipconfig
ping <destination-ip>
```

---

# 19. Troubleshooting Checklist

If a ping fails, check the following.

### Step 1 — Check the PC IP

```text
ipconfig
```

Make sure the PC has the correct IP address.

---

### Step 2 — Check the VLAN

On the switch:

```text
show vlan brief
```

Make sure the PC's port is assigned to the correct VLAN.

---

### Step 3 — Check the Trunk

Use:

```text
show interfaces trunk
```

Make sure the required VLAN is allowed on the trunk.

---

### Step 4 — Check Router Interfaces

Use:

```text
show ip interface brief
```

Interfaces should normally show:

```text
up
up
```

---

### Step 5 — Check DHCP

Use:

```text
show ip dhcp binding
```

If the PC did not receive an IP address, check the DHCP configuration.

---

### Step 6 — Check Routing

Use:

```text
show ip route
```

Make sure the router knows how to reach the destination network.

---

### Step 7 — Test Step by Step

Instead of immediately testing the final destination, test each part:

```text
PC → Default Gateway
```

Then:

```text
PC → Another VLAN Gateway
```

Then:

```text
PC → Server
```

Then:

```text
PC → Remote Router
```

Then:

```text
PC → Remote Network
```

This makes troubleshooting easier.

---

# 20. Final Verification Summary

The Enterprise Multi-VLAN Network successfully demonstrates:

* VLAN creation
* VLAN segmentation
* Access ports
* Trunking
* Router-on-a-Stick
* Inter-VLAN routing
* DHCP
* DNS configuration
* Static IP addressing
* Router-to-router communication
* `/30` subnetting
* Static routing
* Default routing
* Loopback interfaces
* Server connectivity
* End-to-end network verification
* Network troubleshooting

---

# 21. Learning Outcome

Through this project, the following networking concepts were practiced:

```text
VLAN
   ↓
Access Port
   ↓
Trunk
   ↓
802.1Q
   ↓
Router-on-a-Stick
   ↓
Inter-VLAN Routing
   ↓
DHCP
   ↓
IP Addressing
   ↓
Router-to-Router Link
   ↓
Static Routing
   ↓
Server Connectivity
   ↓
Network Verification
```

This project combines multiple networking concepts into one enterprise-style Cisco Packet Tracer topology.

---

# 22. Conclusion

The Enterprise Multi-VLAN Network demonstrates how multiple departments can be separated using VLANs while still allowing controlled communication through routing.

The project also demonstrates automatic IP address assignment using DHCP, communication between routers using a `/30` network, static routing between networks, and connectivity to a dedicated server VLAN.

Verification commands and ping tests were used throughout the project to confirm network operation.

```
