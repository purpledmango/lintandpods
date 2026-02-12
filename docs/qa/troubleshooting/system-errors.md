# System Errors & Troubleshooting

Common system-level errors, diagnostics, and solutions.

---

## Service Won't Start

### Question: How do I diagnose why a service won't start?

**Symptoms:**
- `systemctl start service_name` returns error
- Service appears inactive/failed
- Application not accessible

**Diagnostic Steps:**

```bash
# 1. Check service status
systemctl status service_name

# 2. View full error logs
journalctl -u service_name -n 50

# 3. Follow logs in real-time while restarting
journalctl -u service_name -f &
systemctl restart service_name

# 4. Check service configuration
cat /etc/systemd/system/service_name.service

# 5. Verify dependencies
systemctl list-dependencies service_name
```

**Common Causes & Solutions:**

| Cause | Solution |
|-------|----------|
| Port already in use | `lsof -i :port` and kill the process, or change port |
| Permission denied | Check file ownership: `ls -l /path/to/binary` |
| Missing dependencies | `rpm -qa \| grep dependency` or `dnf install dependency` |
| Config file errors | Validate config: `service_name --config-test` or equivalent |
| Insufficient resources | Check disk space and memory: `df -h` and `free -h` |
| SELinux blocking | Check: `ausearch -m denied \| grep service_name` |

**Resolution Example:**
```bash
# If httpd won't start
systemctl status httpd
# Output shows: Address already in use on port 80

# Find what's using port 80
lsof -i :80
# nginx is using it

# Option 1: Stop nginx and start httpd
systemctl stop nginx
systemctl start httpd

# Option 2: Change httpd port in config
sed -i 's/Listen 80/Listen 8080/' /etc/httpd/conf/httpd.conf
systemctl start httpd
```

**Tags:** `#troubleshooting` `#systemd` `#service`

---

## Out of Disk Space

### Question: How do I diagnose and fix out of disk space issues?

**Symptoms:**
- Applications crashing with "No space left on device"
- Cannot create files
- Services stopping unexpectedly

**Diagnostic Steps:**

```bash
# 1. Check overall disk usage
df -h

# 2. Identify largest directories
du -sh /* | sort -hr

# 3. Check specific filesystem
du -sh /var/*
du -sh /home/*
du -sh /tmp/*

# 4. Find large files
find / -type f -size +100M

# 5. Check inode usage
df -i

# 6. Find directories with many files
find / -type d -exec ls -1 {} \; | wc -l
```

**Common Causes & Solutions:**

| Location | Cause | Solution |
|----------|-------|----------|
| `/var/log` | Large log files | Rotate logs, delete old logs |
| `/tmp` | Temporary files not cleaned | Clean: `rm -rf /tmp/*` |
| `/home` | User files accumulating | Archive old files, increase quota |
| `/var/lib/docker` | Docker images/containers | `docker system prune -a` |
| Database directories | Large databases | Archive, compress, or increase volume |

**Resolution Steps:**

```bash
# Option 1: Clean old logs
journalctl --vacuum=50M

# Option 2: Clean Docker
docker system prune -a

# Option 3: Clean package cache
dnf clean all

# Option 4: Find and delete large old files
find /var/log -name "*.log" -mtime +30 -delete

# Option 5: Extend filesystem (if space available on volume group)
lvextend -L +10G /dev/vg0/lv_root
resize2fs /dev/vg0/lv_root
```

**Tags:** `#troubleshooting` `#disk-space` `#storage`

---

## High CPU Usage

### Question: How do I find and fix high CPU usage?

**Symptoms:**
- System is slow and unresponsive
- `top` shows high CPU usage
- Applications running slowly

**Diagnostic Steps:**

```bash
# 1. See processes using most CPU
top -b -n 1 | head -15

# 2. More detailed CPU info
ps aux --sort=-%cpu | head -10

# 3. Real-time monitoring
htop -p PID

# 4. Check CPU usage by user
top -u username

# 5. Check I/O stats for CPU context
sar -u 1 5

# 6. Get process details
ps -eo pid,user,%cpu,%mem,comm --sort=-%cpu | head
```

**Common Causes & Solutions:**

| Cause | Symptoms | Solution |
|-------|----------|----------|
| Runaway process | One process at 100% CPU | Kill process: `kill -9 PID` |
| Busy loop code | Application in infinite loop | Restart application, review code |
| Compilation/build | Short spikes | Wait for completion |
| Too many threads | System thread overhead | Adjust application settings |
| Context switching | High CPU despite low usage per process | Tune scheduler or add CPU |

**Resolution Example:**

```bash
# Find high CPU process
top -b -n 1 | head -12

# Get process details
ps aux | grep PID

# Check what it's doing
strace -p PID -c

# If runaway, kill it
kill -9 PID

# Restart properly
systemctl restart service_name
```

**Tags:** `#troubleshooting` `#performance` `#cpu`

---

## Out of Memory (OOM)

### Question: How do I diagnose and resolve out of memory issues?

**Symptoms:**
- "Cannot allocate memory" errors
- OOM killer messages in logs
- Random process termination

**Diagnostic Steps:**

```bash
# 1. Check memory usage
free -h

# 2. See memory by process
ps aux --sort=-%mem | head

# 3. Detailed memory info
cat /proc/meminfo

# 4. Check OOM killer events
dmesg | tail -20
grep -i oom /var/log/messages

# 5. Memory pressure
cat /proc/sys/vm/overcommit_memory

# 6. Swap usage
swapon -s
```

**Common Causes & Solutions:**

| Cause | Solution |
|-------|----------|
| Memory leak in application | Restart application, update code |
| Too many containers | Reduce container count or add RAM |
| Swap disabled | Enable swap: `swapon /swap` |
| High buffer/cache | Normal, OS will reclaim as needed |
| Large process | Increase RAM or optimize process |

**Resolution Steps:**

```bash
# Option 1: Increase swap
fallocate -l 4G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile

# Option 2: Find memory hog
ps aux --sort=-%mem | head -5

# Option 3: Restart service
systemctl restart service_name

# Option 4: Adjust OOM settings
# Prevent OOM killing important service
echo -n -17 > /proc/PID/oom_score_adj
```

**Tags:** `#troubleshooting` `#performance` `#memory`

---

## Permission Denied Errors

### Question: How do I diagnose and fix "Permission denied" errors?

**Symptoms:**
- Commands fail with "Permission denied"
- Cannot access files or directories
- Application cannot read/write files

**Diagnostic Steps:**

```bash
# 1. Check file permissions
ls -la /path/to/file

# 2. Check current user
whoami
id

# 3. Check file ownership
stat /path/to/file

# 4. Check group membership
groups username

# 5. Check SELinux context (if enabled)
getenforce
ls -Z /path/to/file

# 6. Check ACLs
getfacl /path/to/file
```

**Common Causes & Solutions:**

| Cause | Solution |
|-------|----------|
| Wrong file owner | `chown user:group /file` |
| Wrong permissions | `chmod 755 /file` or `chmod 644 /file` |
| Wrong group | Add user to group: `usermod -aG groupname user` |
| SELinux context | Restore: `restorecon /file` |
| ACL restrictions | Check with `getfacl` |

**Resolution Examples:**

```bash
# Scenario 1: Execute permission
# Error: Permission denied: /usr/local/bin/script.sh
chmod +x /usr/local/bin/script.sh

# Scenario 2: File ownership
# Error: cannot open file for writing
sudo chown $USER:$USER file.txt
chmod 644 file.txt

# Scenario 3: Directory permission
# Error: cannot enter directory
sudo chmod 755 /directory

# Scenario 4: SELinux
# Error: httpd cannot read /var/www/html files
restorecon -R /var/www/html

# Scenario 5: Add user to group (requires login)
usermod -aG docker username
# User must log out and back in
```

**Tags:** `#troubleshooting` `#permissions` `#security`

---

## Service Crashing Repeatedly

### Question: Why does my service keep crashing?

**Symptoms:**
- Service restarts every few seconds
- `systemctl status` shows rapid restart attempts
- Error logs show repeated crash

**Diagnostic Steps:**

```bash
# 1. Check status and restart count
systemctl status service_name

# 2. View recent logs
journalctl -u service_name -n 100

# 3. Follow logs during crash cycle
journalctl -u service_name -f

# 4. Check restart policy
systemctl cat service_name | grep -i restart

# 5. Check system resources
free -h
df -h
ps aux | wc -l

# 6. Check service dependencies
systemctl list-dependencies service_name
```

**Common Causes & Solutions:**

| Cause | Signal | Solution |
|-------|--------|----------|
| Missing config | Signal 1 (SIGHUP) | Verify config file exists and is valid |
| Bad config | exit code 1 | Check config syntax |
| Missing dependency | Signal 6 (SIGABRT) | Ensure dependency services are running |
| Resource exhausted | Signal 9 (SIGKILL) | Increase resources or optimize |
| Port conflict | Cannot bind port | Find using process with `lsof -i :port` |
| File not found | ENOENT error | Verify paths in config |

**Resolution Steps:**

```bash
# Set restart limit to prevent restart loop
sudo vi /etc/systemd/system/service_name.service
# Add to [Service] section:
# StartLimitInterval=600
# StartLimitBurst=3

# Reload and test
sudo systemctl daemon-reload
sudo systemctl restart service_name
sudo systemctl status service_name
```

**Tags:** `#troubleshooting` `#systemd` `#crash`

---

**Tags:** `#troubleshooting` `#system-errors` `#diagnostics`
