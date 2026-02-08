# Networking

Network configuration, troubleshooting, and command references for RHEL/Linux.

---

## Quick Links

### Commands
- [nmcli - NetworkManager CLI](commands/nmcli.md)
- [IP Command Reference](commands/ip-command.md)
- [Firewall Commands](commands/firewall-cmd.md)
- [Tcpdump - Packet Capture](commands/tcpdump.md)

### Troubleshooting
- [Connectivity Issues](troubleshooting/connectivity.md)
- [DNS Problems](troubleshooting/dns-issues.md)
- [Performance Issues](troubleshooting/performance.md)

### Basics
- [OSI Model](basics/osi-model.md)
- [IP Addressing](basics/ip-addressing.md)
- [Subnetting](basics/subnetting.md)

---

## Common Tasks

### Configure Static IP
```bash
nmcli connection modify ens33 \
  ipv4.method manual \
  ipv4.addresses "192.168.1.100/24" \
  ipv4.gateway "192.168.1.1" \
  ipv4.dns "8.8.8.8"
```
[Full Guide →](commands/nmcli.md#configure-static-ip)

### Troubleshoot Connection
```bash
# Quick diagnostic
ip addr show
ip route show
ping -c 4 8.8.8.8
```
[Full Guide →](troubleshooting/connectivity.md)

---

## Browse by Tag

- #networking
- #configuration
- #troubleshooting
- #commands
