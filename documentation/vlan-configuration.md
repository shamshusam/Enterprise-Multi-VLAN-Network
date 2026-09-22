````markdown
# VLAN Configuration

## 1. What is a VLAN?

VLAN stands for **Virtual Local Area Network**.

A VLAN is used to logically divide one physical network into multiple separate networks.

For example, in this project we have different departments such as:

- HR
- IT
- Finance
- Management
- Servers

Instead of keeping all devices in one network, we separated them using VLANs.

---

## 2. Why Did We Use VLANs?

VLANs were used in this project to:

- Separate different departments
- Reduce broadcast traffic
- Improve network organization
- Improve network security
- Make the network easier to manage
- Create separate logical networks using the same physical switches

For example, an HR PC belongs to VLAN 10, while an IT PC belongs to VLAN 20.

---

## 3. VLANs Used in This Project

| VLAN ID | Department | Network | Default Gateway |
|---|---|---|---|
| 10 | HR | 192.168.10.0/24 | 192.168.10.1 |
| 20 | IT | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Finance | 192.168.30.0/24 | 192.168.30.1 |
| 40 | Management | 192.168.40.0/24 | 192.168.40.1 |
| 50 | Servers | 192.168.50.0/24 | 192.168.50.1 |

---

## 4. Network Addressing

The project uses a `/24` subnet for each VLAN.

The subnet mask is:

```text
255.255.255.0
````

For example:

```text
VLAN 10
Network: 192.168.10.0/24
Gateway: 192.168.10.1

VLAN 20
Network: 192.168.20.0/24
Gateway: 192.168.20.1
```

Each VLAN has its own IP network.

---

## 5. Creating VLANs

The VLANs were created on all switches.

Configuration:

```text
enable
configure terminal

vlan 10
name HR
exit

vlan 20
name IT
exit

vlan 30
name FINANCE
exit

vlan 40
name MANAGEMENT
exit

vlan 50
name SERVERS
exit

end
write memory
```

---

## 6. What Happens When a VLAN Is Created?

When a VLAN is created on a switch, the switch creates a separate logical broadcast domain.

For example:

```text
VLAN 10 → HR
VLAN 20 → IT
VLAN 30 → Finance
```

Devices in VLAN 10 are logically separated from devices in VLAN 20 and VLAN 30.

They cannot communicate directly between VLANs without a Layer 3 device such as a router.

---

# 7. Access Ports

An access port is a switch port assigned to one VLAN.

End devices such as PCs are normally connected to access ports.

For example:

```text
PC0
  |
  |
SW1 Fa0/1
  |
VLAN 10
```

PC0 therefore becomes a member of VLAN 10.

---

## 8. Access Port Configuration on SW1

SW1 contains three PCs.

### PC0 → VLAN 10

```text
interface fa0/1
switchport mode access
switchport access vlan 10
no shutdown
```

### PC1 → VLAN 20

```text
interface fa0/2
switchport mode access
switchport access vlan 20
no shutdown
```

### PC2 → VLAN 30

```text
interface fa0/3
switchport mode access
switchport access vlan 30
no shutdown
```

---

## 9. Access Port Configuration on SW2

### PC3 → VLAN 10

```text
interface fa0/1
switchport mode access
switchport access vlan 10
no shutdown
```

### PC4 → VLAN 20

```text
interface fa0/2
switchport mode access
switchport access vlan 20
no shutdown
```

### PC5 → VLAN 40

```text
interface fa0/3
switchport mode access
switchport access vlan 40
no shutdown
```

---

## 10. Access Port Configuration on SW3

### PC6 → VLAN 10

```text
interface fa0/1
switchport mode access
switchport access vlan 10
no shutdown
```

### PC7 → VLAN 30

```text
interface fa0/2
switchport mode access
switchport access vlan 30
no shutdown
```

### PC8 → VLAN 40

```text
interface fa0/3
switchport mode access
switchport access vlan 40
no shutdown
```

---

## 11. Access Port Configuration on Core Switch

The Core Switch has additional PCs and the server.

### PC9 → VLAN 20

```text
interface fa0/5
switchport mode access
switchport access vlan 20
no shutdown
```

### PC10 → VLAN 30

```text
interface fa0/6
switchport mode access
switchport access vlan 30
no shutdown
```

### PC11 → VLAN 40

```text
interface fa0/7
switchport mode access
switchport access vlan 40
no shutdown
```

### Server → VLAN 50

```text
interface fa0/8
switchport mode access
switchport access vlan 50
no shutdown
```

---

# 12. What Happens to a PC Connected to an Access Port?

When a PC is connected to an access port, the switch places the PC into the VLAN configured on that port.

For example:

```text
PC0
 |
SW1 Fa0/1
 |
VLAN 10
 |
192.168.10.0/24
```

PC0 becomes part of the HR network.

Another example:

```text
PC1
 |
SW1 Fa0/2
 |
VLAN 20
 |
192.168.20.0/24
```

PC1 becomes part of the IT network.

---

# 13. VLAN Communication

Devices in the same VLAN can communicate directly at Layer 2 if their IP configuration allows it.

For example:

```text
PC0 → VLAN 10
PC3 → VLAN 10
```

Both belong to VLAN 10, so they are in the same logical network.

However:

```text
PC0 → VLAN 10
PC1 → VLAN 20
```

These devices are in different VLANs.

They require **inter-VLAN routing** to communicate.

---

# 14. Why Do We Need Inter-VLAN Routing?

VLANs separate networks.

Therefore:

```text
VLAN 10
192.168.10.0/24
```

and

```text
VLAN 20
192.168.20.0/24
```

are different IP networks.

A router is required to forward traffic between them.

In this project, Router1 performs this function using **Router-on-a-Stick**.

The detailed router configuration is documented separately in:

```text
documentation/router-configuration.md
```

---

# 15. VLAN Trunking

A trunk link is used to carry traffic belonging to multiple VLANs over a single physical link.

In this project, trunk links connect:

```text
SW1 → Core-SW
SW2 → Core-SW
SW3 → Core-SW
Core-SW → Router1
```

The trunk links carry:

```text
VLAN 10
VLAN 20
VLAN 30
VLAN 40
VLAN 50
```

Example trunk configuration:

```text
interface fa0/24
switchport mode trunk
switchport trunk allowed vlan 10,20,30,40,50
no shutdown
```

---

# 16. Why Did We Use Trunking?

Without trunking, each VLAN would require a separate physical connection.

Trunking allows multiple VLANs to travel through one physical link.

For example:

```text
              VLAN 10
                 |
              VLAN 20
                 |
SW1 ========= Core-SW
                 |
              VLAN 30
                 |
              VLAN 40
                 |
              VLAN 50
```

The single trunk link can carry traffic from multiple VLANs.

---

# 17. How VLAN Traffic Travels

Suppose PC0 belongs to VLAN 10.

Its traffic can travel like this:

```text
PC0
 ↓
SW1
 ↓
Trunk Link
 ↓
Core-SW
 ↓
Router1
```

The VLAN information is maintained while the traffic crosses the trunk.

Router1 can then route the traffic to another VLAN.

---

# 18. VLAN Verification

The most important command used to verify VLAN configuration is:

```text
show vlan brief
```

This command displays:

* VLAN ID
* VLAN name
* VLAN status
* Ports assigned to each VLAN

Example:

```text
VLAN 10    HR
VLAN 20    IT
VLAN 30    FINANCE
VLAN 40    MANAGEMENT
VLAN 50    SERVERS
```

---

# 19. Verify Access Ports

Use:

```text
show vlan brief
```

Check that the correct ports appear under the correct VLAN.

For example:

```text
Fa0/1 → VLAN 10
Fa0/2 → VLAN 20
Fa0/3 → VLAN 30
```

If a port appears under the wrong VLAN, the PC may not receive the expected IP address or communicate correctly.

---

# 20. Verify Trunk Ports

Use:

```text
show interfaces trunk
```

This command verifies whether the switch port is operating as a trunk.

It also shows which VLANs are allowed on the trunk.

Expected VLANs:

```text
10
20
30
40
50
```

---

# 21. Testing VLAN Connectivity

After configuring the VLANs and routing, connectivity can be tested using:

```text
ping
```

For example, from a PC:

```text
ping 192.168.10.1
```

The gateway should respond if the PC and VLAN configuration are working correctly.

---

# 22. Example Connectivity Test

For a PC in VLAN 10:

```text
PC
IP Address: 192.168.10.x
Gateway: 192.168.10.1
```

Test the gateway:

```text
ping 192.168.10.1
```

If the ping succeeds, the PC can communicate with its default gateway.

---

# 23. Common VLAN Problems

## Problem 1: PC Does Not Get an IP Address

Possible causes:

* Wrong VLAN assigned to the access port
* Trunk not configured
* VLAN does not exist
* DHCP configuration problem
* Cable or interface problem

Useful commands:

```text
show vlan brief
show interfaces trunk
show ip dhcp binding
```

---

## Problem 2: Wrong VLAN Assignment

Check:

```text
show vlan brief
```

Make sure the PC's switch port belongs to the correct VLAN.

For example:

```text
PC0 → Fa0/1 → VLAN 10
```

---

## Problem 3: Trunk Is Not Working

Check:

```text
show interfaces trunk
```

Make sure the required VLANs are allowed:

```text
10,20,30,40,50
```

---

## Problem 4: Different VLANs Cannot Communicate

Check:

* Router-on-a-Stick configuration
* Router subinterfaces
* Default gateway
* Trunk between switch and router
* IP addressing

Useful command:

```text
show ip interface brief
```

---

# 24. VLAN Configuration Summary

The VLAN implementation in this project follows this structure:

```text
VLAN 10 → HR
VLAN 20 → IT
VLAN 30 → Finance
VLAN 40 → Management
VLAN 50 → Servers
```

Each VLAN has its own IP network:

```text
VLAN 10 → 192.168.10.0/24
VLAN 20 → 192.168.20.0/24
VLAN 30 → 192.168.30.0/24
VLAN 40 → 192.168.40.0/24
VLAN 50 → 192.168.50.0/24
```

---

# 25. What I Learned

Through this VLAN configuration, I learned:

* What VLANs are
* Why VLANs are used
* How to create VLANs
* How to assign switch ports to VLANs
* Difference between access and trunk ports
* How VLAN traffic travels through trunk links
* Why different VLANs require routing
* How to verify VLAN configuration
* How to troubleshoot VLAN problems

---

# 26. Important Commands

```text
show vlan brief
show interfaces trunk
show interfaces status
show running-config
show ip interface brief
ping <IP-address>
```

These commands are useful for checking and troubleshooting VLAN configurations.

---

## Conclusion

VLANs were used in this enterprise network to logically separate different departments.

The VLANs provide network segmentation, while trunking allows multiple VLANs to travel across shared links.

Inter-VLAN routing is then used to allow controlled communication between the different VLAN networks.

```

After pasting, click **Commit changes**.

Then tell me **done** — we'll create `router-configuration.md` next.
```
