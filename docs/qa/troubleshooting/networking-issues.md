# Networking Issues & Troubleshooting

Common networking problems, diagnostics, and solutions.

---

## Cannot Reach Host

### Question: How do I diagnose why I can't reach a host?

**Symptoms:**
- Ping times out
- Connection refused or connection timeout
- "No route to host" error

**Diagnostic Steps:**

```bash
# 1. Basic connectivity test
ping -c 4 192.168.1.100

# 2. Check routing
ip route show
traceroute 192.168.1.100

# 3. Check ARP table
arp -a
arp -an | grep 192.168.1.100

# 4. Check local interface
ip addr show
ip link show

# 5. Verify firewall rules
sudo firewall-cmd --list-all
sudo iptables -L -n

# 6. Check network statistics
ss -tuln | grep LISTEN
netstat -an | grep ESTABLISHED
```

**Common Causes & Solutions:**

| Cause | Test | Solution |
|-------|------|----------|
| Interface down | `ip link show` | Bring up: `sudo ip link set eth0 up` |
| No route to destination | `ip route show` | Add route: `sudo ip route add 10.0.0.0/8 via 192.168.1.1` |
| Firewall blocking | `firewall-cmd --list-all` | Open port: `sudo firewall-cmd --add-port=22/tcp` |
| DNS not resolving | `nslookup host` | Check DNS, update /etc/resolv.conf |
| Host unreachable | `traceroute host` | Check network connectivity upstream |
| ARP issues | `arp -a` | Clear and rebuild ARP table |

**Tags:** `#troubleshooting` `#networking` `#connectivity`

---

## DNS Not Resolving

### Question: Why can't I resolve domain names?

**Symptoms:**
- `nslookup` returns "server can't find"
- `ping hostname` fails but `ping IP` works
- Slow DNS responses

**Diagnostic Steps:**

```bash
# 1. Test DNS resolution
nslookup example.com
dig example.com
host example.com

# 2. Check DNS configuration
cat /etc/resolv.conf

# 3. Test with specific nameserver
nslookup example.com 8.8.8.8

# 4. Check DNS service
systemctl status systemd-resolved
systemctl status named

# 5. View DNS cache
systemd-resolve --statistics

# 6. Check network connectivity to DNS server
ping -c 1 8.8.8.8
```

**Common Causes & Solutions:**

| Cause | Solution |
|-------|----------|
| DNS server unreachable | Update nameservers in /etc/resolv.conf |
| DNS service stopped | `systemctl start systemd-resolved` |
| Invalid /etc/resolv.conf | Add nameservers: `nameserver 8.8.8.8` |
| Network interface down | Bring up interface |
| DNS cache issues | `systemd-resolve --flush-caches` |

**Resolution:**
```bash
# Add Google DNS temporarily
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf

# Or update NetworkManager (RHEL/CentOS)
sudo nmcli connection modify ens33 ipv4.dns "8.8.8.8 8.8.4.4"
sudo nmcli connection up ens33
```

**Tags:** `#troubleshooting` `#networking` `#dns`

---

## Port Not Accessible

### Question: Why is a port not accessible?

**Symptoms:**
- `telnet host port` times out
- Connection refused
- Port shows as LISTEN but not reachable

**Diagnostic Steps:**

```bash
# 1. Check if port is listening
ss -tuln | grep :8080
netstat -tuln | grep LISTEN

# 2. Verify service is running
systemctl status service_name
ps aux | grep process_name

# 3. Check firewall
sudo firewall-cmd --list-ports
sudo firewall-cmd --list-services

# 4. Test locally
curl localhost:8080
telnet localhost 8080

# 5. Check remote connectivity
ssh user@host
curl http://host:port

# 6. Trace the path
traceroute -T -p 8080 host
```

**Common Causes & Solutions:**

| Cause | Check | Solution |
|-------|-------|----------|
| Service not running | `systemctl status` | Start service: `systemctl start service` |
| Firewall blocking | `firewall-cmd --list-ports` | Open port: `sudo firewall-cmd --add-port=8080/tcp` |
| Wrong bind address | `ss -tlnp` | Check if bound to localhost only |
| Port already in use | `lsof -i :8080` | Kill process or use different port |
| SELinux blocking | `getsebool -a \| grep httpd` | Set boolean: `setsebool -P httpd_use_nfs on` |

**Resolution Example:**
```bash
# Service running but port not accessible
sudo firewall-cmd --add-port=8080/tcp
sudo firewall-cmd --reload

# Or permanently
sudo firewall-cmd --permanent --add-port=8080/tcp
```

**Tags:** `#troubleshooting` `#networking` `#firewall`

---

## Slow Network / High Latency

### Question: How do I diagnose slow network issues?

**Diagnostic Steps:**

```bash
# 1. Check latency
ping -c 10 8.8.8.8

# 2. Bandwidth test
iperf3 -s  # server
iperf3 -c server_ip  # client

# 3. Network stats
sar -n DEV 1 5
ethtool eth0

# 4. TCP connection state
ss -an | tail -1

# 5. Packet loss
mtr -r -c 100 host

# 6. Check QoS/Traffic shaping
tc -s qdisc show
```

**Common Causes & Solutions:**

| Cause | Solution |
|-------|----------|
| High packet loss | Check cable, switches, distance |
| Congestion | Upgrade link, implement QoS |
| MTU size issues | Check MTU: `ip link show \| grep mtu` |
| DNS delays | Use local DNS caching |
| Routing inefficiency | Review routing table |

**Tags:** `#troubleshooting` `#networking` `#performance`

---

**Tags:** `#troubleshooting` `#networking` `#network-issues`
