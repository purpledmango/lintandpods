---
tags:
  - commands
  - linux
  - docker
  - database
  - deployment
  - quick-reference
---

# Miscellaneous Commands & Notes

Quick reference for various commands, configurations, and snippets used across different tools and platforms.

---

## Linux System Commands

### Update MTU (Maximum Transmission Unit)

**Question: How do I change the MTU size for a network interface?**

**Command:**
```bash
# Set MTU to 1400
ip link set dev eth0 mtu 1400
```

**Make it persistent with nmcli:**
```bash
sudo nmcli connection modify eth0 802-3-ethernet.mtu 1400
sudo nmcli connection up eth0
```

**Verify:**
```bash
ip link show eth0 | grep mtu
```

**Tags:** `#networking` `#mtu` `#performance`

---

### Add IP Address to Interface

**Question: How do I manually add an IP address to an interface?**

**Command:**
```bash
# Add IP address
ip addr add 157.20.214.144/23 dev ens33
```

**Expected Output:**
```bash
# Verify with:
ip addr show ens33
# Should show the new IP address
```

**Note:** This is temporary. For persistent configuration, use `nmcli`.

**Tags:** `#networking` `#ip-address` `#configuration`

---

### Add Default Route

**Question: How do I add a default gateway/route?**

**Command:**
```bash
# Add default route
sudo ip route add default via <GATEWAY_IP> dev <INTERFACE>

# Example:
sudo ip route add default via 192.168.1.1 dev ens33
```

**Verify:**
```bash
ip route show
# Should show: default via 192.168.1.1 dev ens33
```

**Make persistent:**
```bash
sudo nmcli connection modify ens33 ipv4.gateway "192.168.1.1"
sudo nmcli connection up ens33
```

**Tags:** `#networking` `#routing` `#gateway`

---

### Check Folder/Directory Size

**Question: How do I check the size of directories and find what's using disk space?**

**Command:**
```bash
# Check folder sizes in root, sorted by size
du -h --max-depth=1 / | sort -rh
```

**Expected Output:**
```
5.2G    /usr
3.1G    /var
1.5G    /home
512M    /opt
...
```

**Breakdown:**
- `du -h` : Disk usage in human-readable format
- `--max-depth=1` : Only show one level deep
- `sort -rh` : Sort reverse (largest first), human-readable numbers

**Other useful variants:**
```bash
# Check current directory
du -h --max-depth=1 . | sort -rh

# Check specific directory
du -h --max-depth=1 /var | sort -rh

# Include hidden files
du -h --max-depth=1 -a /home | sort -rh

# Show total only
du -sh /var/log
```

**Tags:** `#disk-usage` `#storage` `#troubleshooting`

---

## Database Commands

### MySQL/MariaDB User Management

**Question: How do I list all MySQL users and their hosts?**

**Command:**
```bash
# Connect to MySQL
mysql -u root -p

# List all users
SELECT User, Host FROM mysql.user;
```

**Expected Output:**
```
+------------------+-----------+
| User             | Host      |
+------------------+-----------+
| root             | localhost |
| mysql.sys        | localhost |
| admin            | %         |
| dbuser           | 10.0.0.%  |
+------------------+-----------+
```

**Common user management commands:**
```sql
-- Create new user
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';

-- Grant privileges
GRANT ALL PRIVILEGES ON database.* TO 'username'@'localhost';

-- Show user privileges
SHOW GRANTS FOR 'username'@'localhost';

-- Delete user
DROP USER 'username'@'localhost';

-- Reload privileges
FLUSH PRIVILEGES;
```

**Tags:** `#mysql` `#database` `#user-management`

---

## Docker Commands

### Run MySQL Container with Persistent Storage

**Question: How do I run a MySQL Docker container with data persistence?**

**Command:**
```bash
docker run --name mysql_dock \
  -e MYSQL_ROOT_PASSWORD=YourSecurePassword \
  -v sql_data_persistance:/var/lib/mysql \
  -p 3306:3306 \
  -d mysql:8.0
```

**Explanation:**
- `--name mysql_dock` : Container name
- `-e MYSQL_ROOT_PASSWORD` : Set root password
- `-v sql_data_persistance:/var/lib/mysql` : Named volume for data persistence
- `-p 3306:3306` : Expose MySQL port (host:container)
- `-d` : Run in detached mode
- `mysql:8.0` : MySQL version 8.0 image

**Verify container is running:**
```bash
docker ps | grep mysql_dock
```

**Tags:** `#docker` `#mysql` `#containers`

---

### Access Interactive Container Shell

**Question: How do I get a shell inside a running Docker container?**

**Command:**
```bash
# Using container name
docker exec -it mysql_dock sh

# Or use container ID
docker exec -it <container_id> sh

# For bash (if available)
docker exec -it mysql_dock bash

# For specific user
docker exec -it -u root mysql_dock bash
```

**Common tasks inside container:**
```bash
# Check MySQL is running
ps aux | grep mysql

# View MySQL logs
tail -f /var/log/mysql/error.log

# Connect to MySQL
mysql -u root -p

# Check disk usage
df -h
```

**Exit container:**
```bash
exit
# or press Ctrl+D
```

**Tags:** `#docker` `#troubleshooting` `#debugging`

---

## Deployment Commands

### Deploy Next.js Application with PM2

**Question: How do I deploy and manage a Next.js app with PM2?**

**Command:**
```bash
# Start Next.js app with PM2
pm2 start npm --name "<app_name>" -- start

# Example:
pm2 start npm --name "fotihive-frontend" -- start
```

**Useful PM2 commands:**
```bash
# List all running apps
pm2 list

# View logs
pm2 logs fotihive-frontend

# Restart app
pm2 restart fotihive-frontend

# Stop app
pm2 stop fotihive-frontend

# Delete from PM2
pm2 delete fotihive-frontend

# Save PM2 configuration
pm2 save

# Setup PM2 to start on boot
pm2 startup
# Run the command it outputs

# Monitor in real-time
pm2 monit
```

**PM2 ecosystem file (recommended):**

Create `ecosystem.config.js`:
```javascript
module.exports = {
  apps: [{
    name: 'fotihive-frontend',
    script: 'npm',
    args: 'start',
    cwd: '/path/to/app',
    instances: 1,
    autorestart: true,
    watch: false,
    max_memory_restart: '1G',
    env: {
      NODE_ENV: 'production',
      PORT: 3000
    }
  }]
}
```

**Start with ecosystem file:**
```bash
pm2 start ecosystem.config.js
```

**Tags:** `#deployment` `#nodejs` `#pm2` `#nextjs`

---

## File Transfer & Synchronization

### Rsync - Remote Sync Files

**Question: How do I sync files between servers using rsync?**

**Command:**
```bash
# Sync entire directory to remote server
rsync -avz /var/www/ root@server:/var/www/
```

**Explanation:**
- `-a` : Archive mode (preserves permissions, timestamps, etc.)
- `-v` : Verbose output
- `-z` : Compress data during transfer

**Common rsync examples:**
```bash
# Sync with delete (remove files on destination not in source)
rsync -avz --delete /var/www/ root@server:/var/www/

# Dry run (see what would be transferred)
rsync -avz --dry-run /var/www/ root@server:/var/www/

# Show progress
rsync -avz --progress /var/www/ root@server:/var/www/

# Exclude certain files/directories
rsync -avz --exclude 'node_modules' --exclude '.git' \
  /var/www/ root@server:/var/www/

# Sync over SSH on custom port
rsync -avz -e "ssh -p 2222" /var/www/ root@server:/var/www/

# Bandwidth limit (in KB/s)
rsync -avz --bwlimit=1000 /var/www/ root@server:/var/www/
```

**Verify sync:**
```bash
# On remote server
ls -la /var/www/
du -sh /var/www/
```

**Tags:** `#rsync` `#file-transfer` `#backup` `#deployment`

---

## Monitoring Tools

### Nagios Access

**Service:** Nagios Monitoring

**Access Details:**
```
URL: http://your-nagios-server/nagios
Username: nagiosadmin
Password: [Stored securely - not in plain text]
```

**Common Nagios tasks:**
- View service status
- Check host uptime
- Configure alerts
- View performance graphs
- Schedule maintenance windows

**Configuration files location:**
```bash
/usr/local/nagios/etc/nagios.cfg
/usr/local/nagios/etc/objects/
```

**Restart Nagios:**
```bash
sudo systemctl restart nagios
```

**Tags:** `#monitoring` `#nagios` `#infrastructure`

---

## Quick Reference Snippets

### Common One-Liners

**Check open ports:**
```bash
ss -tuln | grep LISTEN
netstat -tuln | grep LISTEN
```

**Find process using port:**
```bash
sudo lsof -i :8080
sudo ss -tulnp | grep :8080
```

**Check service status:**
```bash
systemctl status servicename
journalctl -u servicename -f
```

**Disk usage:**
```bash
df -h                    # Filesystem usage
du -sh *                 # Directory sizes
du -h --max-depth=1 /
```

**Memory usage:**
```bash
free -h
top
htop
```

**Find large files:**
```bash
find / -type f -size +100M 2>/dev/null
```

**Check system info:**
```bash
uname -a                 # Kernel info
cat /etc/os-release      # OS info
hostnamectl              # Hostname and system info
```

**Tags:** `#quick-reference` `#one-liners` `#troubleshooting`

---

## Tips & Best Practices

!!! tip "Security Note"
    Never store passwords in plain text in scripts or documentation. Use environment variables or secret management tools.

!!! warning "Production Safety"
    Always test commands on a staging environment before running on production systems.

!!! note "Documentation"
    Keep notes of custom configurations and non-standard setups for future reference.

---

## See Also

- [Networking Commands](../networking/commands/nmcli.md)
- [Troubleshooting Guide](../networking/troubleshooting/connectivity.md)
- [RHEL Configuration](../rhel/configuration/network-config.md)

---

**Last Updated:** 2024-02-09  
**Category:** Quick Reference & Miscellaneous
