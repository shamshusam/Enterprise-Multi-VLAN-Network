````markdown
# DHCP Configuration

## 1. What is DHCP?

DHCP stands for **Dynamic Host Configuration Protocol**.

DHCP automatically provides network configuration to devices such as PCs.

A DHCP server can provide:

- IP address
- Subnet mask
- Default gateway
- DNS server

In this project, Router1 acts as the DHCP server.

---

## 2. Why Did We Use DHCP?

We used DHCP so that PCs can automatically receive their network configuration.

Without DHCP, every PC would need to be configured manually.

For example, instead of manually entering:

```text
IP Address: 192.168.10.11
Subnet Mask: 255.255.255.0
Gateway: 192.168.10.1
DNS: 8.8.8.8
````

DHCP automatically provides these values to the PC.

---

## 3. DHCP Networks

DHCP was configured for four user VLANs:

| VLAN | Department | Network         | Gateway      |
| ---- | ---------- | --------------- | ------------ |
| 10   | HR         | 192.168.10.0/24 | 192.168.10.1 |
| 20   | IT         | 192.168.20.0/24 | 192.168.20.1 |
| 30   | Finance    | 192.168.30.0/24 | 192.168.30.1 |
| 40   | Management | 192.168.40.0/24 | 192.168.40.1 |

VLAN 50 is used for the server and uses a static IP address.

---

# 4. Excluding IP Addresses

The first few addresses of each network were excluded from DHCP.

Configuration:

```text
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10
ip dhcp excluded-address 192.168.40.1 192.168.40.10
```

This prevents DHCP from assigning these addresses to PCs.

For example, in VLAN 10:

```text
192.168.10.1
to
192.168.10.10
```

are excluded.

The DHCP pool can therefore begin assigning addresses from:

```text
192.168.10.11
```

---

# 5. DHCP Pool for HR

The HR DHCP pool is:

```text
ip dhcp pool HR
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
exit
```

This pool provides addresses from the HR network.

The default gateway is:

```text
192.168.10.1
```

The DNS server provided to the PC is:

```text
8.8.8.8
```

---

# 6. DHCP Pool for IT

The IT DHCP pool is:

```text
ip dhcp pool IT
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
dns-server 8.8.8.8
exit
```

The default gateway is:

```text
192.168.20.1
```

---

# 7. DHCP Pool for Finance

The Finance DHCP pool is:

```text
ip dhcp pool FINANCE
network 192.168.30.0 255.255.255.0
default-router 192.168.30.1
dns-server 8.8.8.8
exit
```

The default gateway is:

```text
192.168.30.1
```

---

# 8. DHCP Pool for Management

The Management DHCP pool is:

```text
ip dhcp pool MANAGEMENT
network 192.168.40.0 255.255.255.0
default-router 192.168.40.1
dns-server 8.8.8.8
exit
```

The default gateway is:

```text
192.168.40.1
```

---

# 9. Complete DHCP Configuration

The complete DHCP configuration on Router1 is:

```text
enable
configure terminal

ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10
ip dhcp excluded-address 192.168.30.1 192.168.30.10
ip dhcp excluded-address 192.168.40.1 192.168.40.10

ip dhcp pool HR
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
exit

ip dhcp pool IT
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
dns-server 8.8.8.8
exit

ip dhcp pool FINANCE
network 192.168.30.0 255.255.255.0
default-router 192.168.30.1
dns-server 8.8.8.8
exit

ip dhcp pool MANAGEMENT
network 192.168.40.0 255.255.255.0
default-router 192.168.40.1
dns-server 8.8.8.8
exit

end
write memory
```

---

# 10. What Happens When a PC Requests DHCP?

When a PC is configured for DHCP, it requests network information automatically.

The basic process is:

```text
PC
 ↓
DHCP Discover
 ↓
Router1
 ↓
DHCP Offer
 ↓
DHCP Request
 ↓
DHCP Acknowledgement
 ↓
PC receives network configuration
```

The PC then receives an IP address, subnet mask, default gateway, and DNS server.

---

# 11. Example DHCP Assignment

For a PC in VLAN 10, the PC may receive:

```text
IP Address:    192.168.10.11
Subnet Mask:   255.255.255.0
Default Gateway: 192.168.10.1
DNS Server:    8.8.8.8
```

Another PC in VLAN 20 may receive:

```text
IP Address:    192.168.20.11
Subnet Mask:   255.255.255.0
Default Gateway: 192.168.20.1
DNS Server:    8.8.8.8
```

---

# 12. What Does `default-router` Mean?

The command:

```text
default-router 192.168.10.1
```

tells the PC which device should be used as its default gateway.

For VLAN 10:

```text
PC → 192.168.10.1 → Router1
```

The router can then forward traffic to other networks.

---

# 13. What Does `dns-server` Mean?

The command:

```text
dns-server 8.8.8.8
```

tells the PC which DNS server to use.

DNS translates domain names into IP addresses.

For example:

```text
google.com
     ↓
DNS Server
     ↓
IP address
```

The PC can ask the configured DNS server to resolve a domain name.

---

# 14. Configuring PCs for DHCP

On each PC:

1. Open the PC.
2. Select **Desktop**.
3. Select **IP Configuration**.
4. Select **DHCP**.

The PC should automatically receive its IP configuration.

---

# 15. Verify DHCP on a PC

Open:

```text
PC → Desktop → Command Prompt
```

Use:

```text
ipconfig
```

The output should show an IP address, subnet mask, and default gateway.

For example:

```text
IP Address:      192.168.10.11
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.10.1
```

---

# 16. Verify DHCP on Router1

Use:

```text
show ip dhcp binding
```

This displays the IP addresses that Router1 has assigned to clients.

Example:

```text
IP address       Client-ID
192.168.10.11    ...
192.168.20.11    ...
192.168.30.11    ...
192.168.40.11    ...
```

---

# 17. View DHCP Pools

Use:

```text
show ip dhcp pool
```

This displays information about the configured DHCP pools.

The pools should include:

```text
HR
IT
FINANCE
MANAGEMENT
```

---

# 18. Verify DHCP Configuration

Use:

```text
show running-config
```

Look for:

```text
ip dhcp pool HR
ip dhcp pool IT
ip dhcp pool FINANCE
ip dhcp pool MANAGEMENT
```

Also verify the excluded addresses.

---

# 19. DHCP Troubleshooting

## Problem 1: PC Gets a 169.254.x.x Address

A `169.254.x.x` address can indicate that the PC did not receive an address from DHCP.

Check:

```text
show vlan brief
```

Make sure the PC's switch port belongs to the correct VLAN.

---

## Problem 2: DHCP Pool Does Not Work

Check:

```text
show ip dhcp pool
```

Make sure the correct network is configured.

For example:

```text
network 192.168.10.0 255.255.255.0
```

---

## Problem 3: PC Gets an IP but Cannot Communicate

Check the default gateway:

```text
ipconfig
```

For VLAN 10 it should be:

```text
192.168.10.1
```

Then test:

```text
ping 192.168.10.1
```

---

## Problem 4: PC Does Not Receive an IP

Check:

```text
show vlan brief
show interfaces trunk
show ip dhcp binding
show ip interface brief
```

These commands help identify VLAN, trunk, DHCP, and interface problems.

---

# 20. DHCP Verification Process

The complete verification process is:

```text
1. Configure PC for DHCP
        ↓
2. Check PC IP address
        ↓
3. Check default gateway
        ↓
4. Ping the gateway
        ↓
5. Check DHCP binding on Router1
```

Useful commands:

```text
ipconfig
ping 192.168.10.1
show ip dhcp binding
show ip dhcp pool
```

---

# 21. DHCP and VLAN Relationship

DHCP works together with VLANs in this project.

For example:

```text
PC0
 ↓
SW1
 ↓
VLAN 10
 ↓
Trunk
 ↓
Router1
 ↓
DHCP Pool HR
 ↓
192.168.10.x
```

The VLAN determines which network the PC belongs to, while DHCP automatically provides the IP configuration for that network.

---

# 22. Why Is the Server Not Using DHCP?

The server in this project uses a static IP address.

Server configuration:

```text
IP Address:      192.168.50.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.50.1
```

The server belongs to:

```text
VLAN 50
```

A static IP makes the server address predictable and easier for other devices to access.

---

# 23. DHCP Summary

| VLAN | DHCP Pool  | Gateway      |
| ---- | ---------- | ------------ |
| 10   | HR         | 192.168.10.1 |
| 20   | IT         | 192.168.20.1 |
| 30   | FINANCE    | 192.168.30.1 |
| 40   | MANAGEMENT | 192.168.40.1 |

VLAN 50 uses a static server address.

---

# 24. What I Learned

Through DHCP configuration, I learned:

* What DHCP is
* Why DHCP is used
* How to create a DHCP pool
* How to exclude IP addresses
* How DHCP assigns IP addresses
* What a default gateway does
* What a DNS server does
* How to configure PCs for DHCP
* How to verify DHCP assignments
* How to troubleshoot DHCP problems

---

## Conclusion

DHCP simplifies network configuration by automatically providing IP addresses and other network information to PCs.

In this project, Router1 acts as the DHCP server for VLANs 10, 20, 30, and 40.

This allows the enterprise PCs to automatically receive the correct IP address, subnet mask, default gateway, and DNS server.

```
