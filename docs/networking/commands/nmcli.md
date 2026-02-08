---
tags:
  - networking
  - commands
  - nmcli
  - configuration
---

# nmcli - NetworkManager Command Line Interface

Complete reference for NetworkManager CLI operations in RHEL.

---

## Overview

`nmcli` is the primary command-line tool for managing network connections in RHEL 8/9.

!!! tip "Why nmcli?"
    - Changes are **persistent** (survive reboots)
    - Works with **NetworkManager** (the standard in RHEL 8/9)
    - Supports all connection types: ethernet, wifi, bond, bridge, VLAN, etc.

---

## Quick Reference

| Task | Command |
|------|---------|
| View connections | `nmcli con show` |
| View devices | `nmcli dev status` |
| Up/down connection | `nmcli con up/down <name>` |
| Reload configs | `nmcli con reload` |

---

## View Network Status

### Question: How do I see all network connections?

**Command:**
```bash
nmcli connection show
```

**Expected Output:**
```
NAME    UUID                                  TYPE      DEVICE
ens33   5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  ens33
virbr0  d7e1ccc0-c3a0-4a2d-b7b3-9e6e7d8c9f0e  bridge    virbr0
```

**What it means:**
- `NAME`: Connection profile name
- `UUID`: Unique identifier
- `TYPE`: Connection type (ethernet, wifi, bridge, etc.)
- `DEVICE`: Associated network interface

---

### Question: How do I check device status?

**Command:**
```bash
nmcli device status
```

**Expected Output:**
```
DEVICE  TYPE      STATE      CONNECTION
ens33   ethernet  connected  ens33
virbr0  bridge    connected  virbr0
lo      loopback  unmanaged  --
```

**States explained:**
- `connected`: Device is active and connected
- `disconnected`: Device is available but not connected
- `unmanaged`: NetworkManager doesn't control this device
- `unavailable`: Device is not available (cable unplugged, etc.)

---

### Question: How do I see detailed info about a connection?

**Command:**
```bash
nmcli connection show ens33
```

**Expected Output:**
```
connection.id:                          ens33
connection.uuid:                        5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03
connection.type:                        802-3-ethernet
connection.interface-name:              ens33
ipv4.method:                            manual
ipv4.addresses:                         192.168.1.100/24
ipv4.gateway:                           192.168.1.1
ipv4.dns:                               8.8.8.8,8.8.4.4
IP4.ADDRESS[1]:                         192.168.1.100/24
IP4.GATEWAY:                            192.168.1.1
IP4.ROUTE[1]:                           dst = 0.0.0.0/0, nh = 192.168.1.1
```

---

## Configure Static IP

### Question: How do I set a static IP address?

**Scenario:** Configure ens33 with:
- IP: 192.168.1.100/24
- Gateway: 192.168.1.1
- DNS: 8.8.8.8, 8.8.4.4

**Command:**
```bash
sudo nmcli connection modify ens33 \
  ipv4.method manual \
  ipv4.addresses "192.168.1.100/24" \
  ipv4.gateway "192.168.1.1" \
  ipv4.dns "8.8.8.8 8.8.4.4"

sudo nmcli connection up ens33
```

**Verification:**
```bash
ip addr show ens33
ping -c 4 192.168.1.1
ping -c 4 8.8.8.8
```

!!! warning "Important"
    This will disconnect your current connection briefly when you run `nmcli connection up`

**Tags:** `#static-ip` `#configuration` `#ipv4`

---

### Question: How do I add a secondary IP address?

**Command:**
```bash
sudo nmcli connection modify ens33 \
  +ipv4.addresses "192.168.1.101/24"

sudo nmcli connection up ens33
```

**Verification:**
```bash
ip addr show ens33
```

**Expected Output:**
```
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP>
    inet 192.168.1.100/24 brd 192.168.1.255 scope global noprefixonly ens33
    inet 192.168.1.101/24 brd 192.168.1.255 scope global secondary noprefixonly ens33
```

**Note:** Use `+` to add, `-` to remove.

---

## Configure DHCP

### Question: How do I switch from static to DHCP?

**Command:**
```bash
sudo nmcli connection modify ens33 ipv4.method auto
sudo nmcli connection up ens33
```

**Explanation:**
- `ipv4.method auto` = DHCP
- This removes static IP, gateway, and DNS settings
- System will request IP from DHCP server

**Verification:**
```bash
nmcli connection show ens33 | grep ipv4.method
ip addr show ens33
```

---

## DNS Configuration

### Question: How do I change DNS servers?

**Command:**
```bash
sudo nmcli connection modify ens33 \
  ipv4.dns "1.1.1.1 1.0.0.1"

sudo nmcli connection up ens33
```

**Add additional DNS server:**
```bash
sudo nmcli connection modify ens33 \
  +ipv4.dns "8.8.8.8"
```

**Verification:**
```bash
cat /etc/resolv.conf
dig google.com
```

**Expected Output in /etc/resolv.conf:**
```
nameserver 1.1.1.1
nameserver 1.0.0.1
nameserver 8.8.8.8
```

---

### Question: How do I set DNS search domain?

**Command:**
```bash
sudo nmcli connection modify ens33 \
  ipv4.dns-search "example.com lab.local"

sudo nmcli connection up ens33
```

**What it does:**
- When you `ping server1`, it tries `server1.example.com`
- Useful in corporate environments

---

## Create New Connection

### Question: How do I create a new connection profile?

**Command:**
```bash
sudo nmcli connection add \
  type ethernet \
  con-name office-network \
  ifname ens33 \
  ipv4.method manual \
  ipv4.addresses "192.168.1.50/24" \
  ipv4.gateway "192.168.1.1" \
  ipv4.dns "8.8.8.8"
```

**What this does:**
- Creates a new connection profile named "office-network"
- Configures it with static IP
- Does NOT activate it yet

**Activate:**
```bash
sudo nmcli connection up office-network
```

**Use case:** Multiple network profiles for different locations (office, home, etc.)

---

## Activate/Deactivate Connections

### Question: How do I switch between connection profiles?

**Command:**
```bash
# Deactivate current
sudo nmcli connection down ens33

# Activate different profile
sudo nmcli connection up office-network
```

**Or in one step:**
```bash
sudo nmcli connection up office-network
```

(This automatically deactivates the old one on the same interface)

---

## Delete Connection

### Question: How do I remove a connection profile?

**Command:**
```bash
sudo nmcli connection delete office-network
```

!!! danger "Warning"
    This permanently deletes the connection profile. Make sure it's not active.

**Safe approach:**
```bash
# First deactivate
sudo nmcli connection down office-network

# Then delete
sudo nmcli connection delete office-network
```

---

## Troubleshooting

### Question: Why aren't my changes taking effect?

**Common Issues & Solutions:**

1. **Forgot to activate:**
   ```bash
   sudo nmcli connection up ens33
   ```

2. **Wrong connection name:**
   ```bash
   # List all connections to find correct name
   nmcli connection show
   ```

3. **NetworkManager not running:**
   ```bash
   sudo systemctl status NetworkManager
   sudo systemctl start NetworkManager
   ```

4. **Syntax error in command:**
   ```bash
   # Check connection settings
   nmcli connection show ens33
   ```

---

### Question: How do I reset a connection to defaults?

**Command:**
```bash
# Delete and recreate
sudo nmcli connection delete ens33
sudo nmcli connection add type ethernet con-name ens33 ifname ens33
sudo nmcli connection up ens33
```

**Or reload from file:**
```bash
sudo nmcli connection reload
sudo nmcli connection up ens33
```

---

## Advanced Usage

### Question: How do I configure connection to auto-connect?

**Command:**
```bash
sudo nmcli connection modify ens33 connection.autoconnect yes
```

**Disable auto-connect:**
```bash
sudo nmcli connection modify ens33 connection.autoconnect no
```

---

### Question: How do I set connection priority?

**Command:**
```bash
sudo nmcli connection modify ens33 connection.autoconnect-priority 10
sudo nmcli connection modify wlan0 connection.autoconnect-priority 5
```

**Explanation:**
- Higher number = higher priority
- Useful when you have multiple networks available

---

## Configuration Files

### Question: Where are connection files stored?

**Location:**
```bash
/etc/NetworkManager/system-connections/
```

**View a connection file:**
```bash
sudo cat /etc/NetworkManager/system-connections/ens33.nmconnection
```

**Example content:**
```ini
[connection]
id=ens33
uuid=5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03
type=ethernet
interface-name=ens33

[ipv4]
method=manual
address1=192.168.1.100/24,192.168.1.1
dns=8.8.8.8;8.8.4.4;

[ipv6]
method=auto
```

!!! warning
    While you CAN edit these files directly, it's better to use `nmcli` commands. If you do edit manually, run `nmcli connection reload` after.

---

## Real-World Examples

### Example 1: Home Office Setup

```bash
# Create profile for home office
sudo nmcli connection add \
  type ethernet \
  con-name home-office \
  ifname ens33 \
  ipv4.method manual \
  ipv4.addresses "192.168.1.100/24" \
  ipv4.gateway "192.168.1.1" \
  ipv4.dns "192.168.1.1 8.8.8.8" \
  ipv4.dns-search "home.local"

sudo nmcli connection up home-office
```

### Example 2: Corporate Network Setup

```bash
# Corporate network with static IP
sudo nmcli connection add \
  type ethernet \
  con-name corporate \
  ifname ens33 \
  ipv4.method manual \
  ipv4.addresses "10.0.1.100/24" \
  ipv4.gateway "10.0.1.1" \
  ipv4.dns "10.0.1.10 10.0.1.11" \
  ipv4.dns-search "corp.example.com"

sudo nmcli connection up corporate
```

### Example 3: Quick DHCP Setup

```bash
# Simple DHCP configuration
sudo nmcli connection add \
  type ethernet \
  con-name auto-network \
  ifname ens33

sudo nmcli connection up auto-network
```

---

## Common Error Messages

### "Connection 'ens33' is not available"

**Cause:** Interface doesn't exist or is unavailable

**Solution:**
```bash
# Check available interfaces
ip link show

# Check device status
nmcli device status
```

---

### "Error: Connection activation failed"

**Cause:** Various reasons - check logs

**Solution:**
```bash
# Check NetworkManager logs
sudo journalctl -u NetworkManager -n 50

# Check connection settings
nmcli connection show ens33
```

---

## Tips & Best Practices

!!! tip "Naming Connections"
    Use descriptive names: `home-office`, `corporate-vpn`, `wifi-cafe`
    
    Not: `connection1`, `test`, `new`

!!! tip "Before Making Changes"
    ```bash
    # Save current config
    nmcli connection show ens33 > ~/ens33-backup.txt
    ```

!!! tip "Testing Changes"
    Test with a secondary connection first, not your active SSH connection!

!!! tip "Interactive Mode"
    For complex setups, use `nmtui` (text UI):
    ```bash
    sudo nmtui
    ```

---

## See Also

- [IP Command Reference](ip-command.md)
- [Network Troubleshooting](../troubleshooting/connectivity.md)
- [RHEL Network Configuration](../../rhel/configuration/network-config.md)

---

**Last Updated:** 2024-02-08  
**Tested On:** RHEL 8.x, RHEL 9.x
