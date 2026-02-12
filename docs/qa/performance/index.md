# Performance Optimization & Tuning

CPU, memory, disk, and I/O performance optimization.

---

## CPU Performance Optimization

See: [System Errors - High CPU Usage](../troubleshooting/system-errors.md#high-cpu-usage)

---

## Memory Optimization

### Question: How do I optimize memory usage?

**Diagnostic:**

```bash
# Check memory distribution
cat /proc/meminfo
free -h

# View by process
ps aux --sort=-%mem | head

# Check kernel tuning
cat /proc/sys/vm/swappiness

# Memory pressure
cat /proc/pressure/memory
```

**Optimization Techniques:**

```bash
# Reduce swappiness (prefer RAM)
sudo sysctl -w vm.swappiness=10

# Clear caches safely
sudo sync
echo 3 | sudo tee /proc/sys/vm/drop_caches

# Configure hugepages
cat /proc/meminfo | grep Huge
sudo sysctl -w vm.nr_hugepages=128
```

**Tags:** `#performance` `#memory` `#tuning`

---

## Disk I/O Optimization

### Question: How do I optimize disk I/O performance?

**Diagnostic:**

```bash
# I/O stats
iostat -x 1 5
iotop

# Disk scheduler
cat /sys/block/sda/queue/scheduler

# Check queue depth
cat /sys/block/sda/device/queue_depth
```

**Optimization:**

```bash
# Change scheduler (noop for SSD, cfq for HDD)
echo 'noop' | sudo tee /sys/block/sda/queue/scheduler

# Increase queue depth
echo 64 | sudo tee /sys/block/sda/device/queue_depth

# Enable writeback cache
echo 'write back' | sudo tee /sys/block/sda/cache_type
```

**Tags:** `#performance` `#disk` `#io`

---

## Network Performance Tuning

### Question: How do I optimize network performance?

**Diagnostic:**

```bash
# Test bandwidth
iperf3 -c server_ip

# Network stats
sar -n DEV 1 5
netstat -s

# TCP tuning
cat /proc/sys/net/ipv4/tcp_fin_timeout
```

**Optimization:**

```bash
# Increase TCP backlog
sudo sysctl -w net.core.somaxconn=4096

# Increase TCP buffers
sudo sysctl -w net.ipv4.tcp_rmem="4096 87380 134217728"
sudo sysctl -w net.ipv4.tcp_wmem="4096 65536 134217728"

# Enable TCP fast open
sudo sysctl -w net.ipv4.tcp_fastopen=3
```

**Tags:** `#performance` `#network` `#tuning`

---

## Database Query Optimization

### Question: How do I optimize slow database queries?

See: [Database Issues - Slow Query Performance](../troubleshooting/database-issues.md#slow-query-performance)

---

**Tags:** `#performance` `#optimization` `#tuning`
