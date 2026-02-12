# Cheatsheets & Quick References

Quick reference guides for common commands and operations across different domains.

---

## Linux System Administration

### Networking Cheatsheet
```bash
# Interface Management
ip addr show                    # Show all IPs
ip link show                    # Show interfaces
nmcli device status            # NM device status
ethtool eth0                   # Interface details

# Network Configuration (nmcli)
nmcli connection show          # List connections
nmcli device wifi list         # Available WiFi networks
nmcli device wifi connect SSID password 'pass'  # Connect to WiFi
nmcli connection modify ens33 ipv4.method manual
nmcli connection modify ens33 ipv4.addresses "192.168.1.100/24"
nmcli connection modify ens33 ipv4.gateway "192.168.1.1"
nmcli connection modify ens33 ipv4.dns "8.8.8.8"
nmcli connection up ens33      # Activate connection
nmcli connection down ens33    # Deactivate connection

# Routing
ip route show                  # Show routing table
ip route add 10.0.0.0/8 via 192.168.1.1  # Add route
ip route delete 10.0.0.0/8     # Delete route
sudo ip route add default via 192.168.1.1  # Default gateway

# DNS Configuration
cat /etc/resolv.conf           # Current DNS
nslookup example.com           # DNS lookup
dig example.com +short         # Quick DNS query
getent hosts example.com       # Check DNS resolution
```

### Firewall Cheatsheet (firewalld)
```bash
# Service Status
sudo firewall-cmd --state               # Firewall status
sudo systemctl status firewalld         # Service status
sudo systemctl start firewalld          # Start firewall
sudo systemctl enable firewalld         # Enable at boot

# Listing Rules
sudo firewall-cmd --list-all            # All rules in default zone
sudo firewall-cmd --list-ports          # Open ports
sudo firewall-cmd --list-services       # Open services
sudo firewall-cmd --get-zones           # Available zones
sudo firewall-cmd --get-default-zone    # Default zone

# Adding Rules (Temporary)
sudo firewall-cmd --add-port=8080/tcp   # Add TCP port
sudo firewall-cmd --add-port=5353/udp   # Add UDP port
sudo firewall-cmd --add-service=http    # Add service
sudo firewall-cmd --add-service=https   # Add HTTPS

# Adding Rules (Permanent)
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload              # Reload after permanent changes

# Removing Rules
sudo firewall-cmd --remove-port=8080/tcp
sudo firewall-cmd --remove-service=http
sudo firewall-cmd --permanent --remove-port=8080/tcp

# Zone Management
sudo firewall-cmd --set-default-zone=public
sudo firewall-cmd --change-zone=eth0 --zone=trusted
sudo firewall-cmd --get-zone-of-interface=eth0
```

### SELinux Cheatsheet
```bash
# Check Status
getenforce                     # Current enforcing mode
sestatus                       # Detailed SELinux status
getenforce -P                 # Current and persistent mode

# Change Mode
setenforce Enforcing          # Set to enforcing (temporary)
setenforce Permissive         # Set to permissive (temporary)
# For permanent: Edit /etc/selinux/config and reboot

# Manage Booleans
getsebool -a                  # List all booleans
getsebool httpd_can_sendmail  # Check specific boolean
setsebool httpd_can_sendmail on    # Temporary change
setsebool -P httpd_can_sendmail on # Permanent change

# Manage File Contexts
ls -Z /path                   # Show SELinux context
semanage fcontext -l          # List file contexts
semanage fcontext -l | grep httpd  # Search contexts
restorecon -v /path           # Restore default context
chcon -t httpd_sys_content_t /path  # Change context

# Port Management
semanage port -l              # List port assignments
semanage port -a -t http_port_t -p tcp 8080  # Add port

# Troubleshooting
ausearch -m avc -ts recent    # Recent denials
tail -f /var/log/audit/audit.log | grep denied  # Follow denials
audit2why < /var/log/audit/audit.log  # Explain denials
audit2allow -a -M custompolicy  # Create policy module
semodule -i custompolicy.pp   # Load policy
```

### Disk Operations Cheatsheet
```bash
# Disk Information
lsblk                         # Block device tree
lsblk -f                      # With filesystems
fdisk -l                      # Partition info (must use sudo)
parted -l                     # All partitions
blkid                         # Block device IDs
df -h                         # Disk space usage
du -sh /path                  # Directory size

# Create Partitions
sudo fdisk /dev/sdb          # Interactive partitioning
sudo parted /dev/sdb         # Parted utility

# Format Partitions
sudo mkfs.ext4 /dev/sdb1     # Create ext4 filesystem
sudo mkfs.xfs /dev/sdb1      # Create XFS filesystem
sudo mkswap /dev/sdb1        # Create swap

# Mount/Unmount
sudo mkdir /mnt/data         # Create mount point
sudo mount /dev/sdb1 /mnt/data  # Mount partition
sudo umount /mnt/data        # Unmount
sudo mount -o remount,rw /   # Remount as read-write
mount | grep ext4            # Show mounted ext4

# Persistent Mounting (/etc/fstab)
# Format: device mount point filesystem options dump pass
UUID=xxx /mnt/data ext4 defaults 0 2
sudo mount -a                # Test /etc/fstab

# Disk Monitoring
iostat -x 1 5               # I/O statistics
iotop                       # I/O per process
df -h                       # Free space
hdparm -i /dev/sda         # Drive info
smartctl -a /dev/sda       # Smart status
```

### User & Permission Cheatsheet
```bash
# User Management
useradd -m -s /bin/bash user        # Create user with home
useradd -m -G wheel user            # Create with sudo group
userdel -r user                     # Delete user and home
passwd user                         # Set password
usermod -aG wheel user              # Add to group
usermod -L user                     # Lock user
usermod -U user                     # Unlock user

# Group Management
groupadd groupname                  # Create group
groupdel groupname                  # Delete group
groups user                         # User's groups
id user                            # User ID info

# File Permissions
chmod 755 file                      # rwxr-xr-x
chmod 644 file                      # rw-r--r--
chmod u+x file                      # Add execute for owner
chmod -R 755 /dir                   # Recursive
chown user:group file               # Change owner:group
chown -R user:group /dir            # Recursive
chown :group file                   # Change group only

# Sudoers (use visudo!)
sudo visudo                         # Edit sudoers safely
user ALL=(ALL) NOPASSWD: /usr/bin/systemctl  # No pwd for specific cmd
%wheel ALL=(ALL) ALL                # All wheel group members
```

### Package Management Cheatsheet
```bash
# DNF Operations
sudo dnf search keyword             # Search packages
sudo dnf info package_name          # Package details
sudo dnf install package_name       # Install package
sudo dnf remove package_name        # Remove package
sudo dnf update                     # Update all packages
sudo dnf upgrade                    # Upgrade all packages
sudo dnf clean all                  # Clean cache
sudo dnf list installed | grep vim  # Search installed
sudo dnf history                    # Installation history

# YUM Operations (older systems)
sudo yum search keyword
sudo yum install package_name
sudo yum remove package_name
sudo yum update
sudo yum clean all

# RPM Direct Operations
rpm -qa                             # List all packages
rpm -qa | grep vim                  # Search installed
rpm -qi package_name                # Package information
rpm -ql package_name                # Files in package
rpm -V package_name                 # Verify package
sudo rpm -Fvh package.rpm          # Install/upgrade
sudo rpm -e package_name            # Remove package

# Group Management
sudo dnf grouplist                  # List package groups
sudo dnf groupinfo "Development Tools"  # Group details
sudo dnf groupinstall "Development Tools"  # Install group
```

### Service Management Cheatsheet
```bash
# Service Commands
systemctl status service_name           # Service status
sudo systemctl start service_name       # Start service
sudo systemctl stop service_name        # Stop service
sudo systemctl restart service_name     # Restart
sudo systemctl reload service_name      # Reload config
sudo systemctl enable service_name      # Enable at boot
sudo systemctl disable service_name     # Disable at boot
sudo systemctl is-enabled service_name  # Check if enabled

# Listing Services
systemctl list-units --type=service     # Active services
systemctl list-units --type=service --all  # All services
systemctl list-unit-files               # Service status
systemctl list-timers                   # Scheduled timers
systemctl list-dependencies service_name  # Service dependencies

# Logs
journalctl -u service_name              # Service logs
journalctl -u service_name -f          # Follow logs
journalctl -u service_name --since "1 hour ago"

# Reload Daemon
sudo systemctl daemon-reload            # After config changes
```

### Process Management Cheatsheet
```bash
# View Processes
ps aux                              # All processes
ps aux | grep process_name         # Find process
pgrep process_name                 # Get PID
pidof command_name                 # PID of command
top                                # Real-time monitor
htop                               # Interactive monitor
ps -eo pid,user,%cpu,%mem,comm     # Custom columns

# Kill Processes
kill PID                           # Graceful kill
kill -9 PID                        # Force kill (-SIGKILL)
kill -15 PID                       # Terminate (-SIGTERM)
pkill process_name                 # Kill by name
killall process_name               # Kill all instances

# Process Priority
nice -n 10 command                 # Run with lower priority (-20 to 19)
renice -n 5 -p PID                 # Change priority of running process
renice -n 5 -u username            # Change user's processes priority

# Process Info
cat /proc/PID/cmdline              # Process command line
cat /proc/PID/status               # Process status
cat /proc/PID/io                   # I/O stats
strace -p PID                      # Trace system calls
lsof -p PID                        # Open files by process
```

### SSH & Remote Access Cheatsheet
```bash
# SSH Connection
ssh user@host                      # Basic connection
ssh -p 2222 user@host             # Custom port
ssh -i /path/key.pem user@host    # Specific key
ssh -v user@host                  # Verbose (debug)
ssh -N -L 8080:localhost:80 user@host  # Local forward
ssh -N -R 8080:localhost:80 user@host  # Remote forward

# SSH Key Management
ssh-keygen -t rsa -b 4096         # Generate key pair
ssh-keygen -t ed25519             # Generate modern key
ssh-copy-id -i ~/.ssh/id_rsa.pub user@host  # Copy key
chmod 600 ~/.ssh/id_rsa           # Key permissions (important!)
chmod 644 ~/.ssh/id_rsa.pub       # Public key permissions

# SSH Agent
ssh-agent bash                     # Start agent in subshell
eval "$(ssh-agent)"               # Start agent in current shell
ssh-add ~/.ssh/id_rsa             # Add key to agent
ssh-add -l                        # List keys in agent
ssh-add -D                        # Remove all keys from agent

# SSH Config
cat ~/.ssh/config                 # SSH config file
# Example config entry:
# Host myserver
#     HostName 192.168.1.100
#     User username
#     IdentityFile ~/.ssh/id_rsa
#     Port 2222

# File Transfer
scp file.txt user@host:/tmp/      # Copy to remote
scp user@host:/tmp/file.txt .     # Copy from remote
scp -r /local/dir user@host:/tmp/ # Recursive copy
sftp user@host                    # SFTP interactive
```

### Log Management Cheatsheet
```bash
# Systemd Logs (journalctl)
journalctl                              # All logs
journalctl -n 50                       # Last 50 lines
journalctl -f                          # Follow in real-time
journalctl -u service_name             # Service-specific logs
journalctl -u ssh -n 20                # Last 20 SSH logs
journalctl -p err                      # Error level only
journalctl -p warning                  # Warning and higher
journalctl -S "2024-01-01"             # Since date
journalctl --since "1 hour ago"        # Last hour
journalctl -k                          # Kernel messages
journalctl -u service -S "1 hour ago" -f  # Service logs (1 hr, follow)

# Traditional Logs
tail -f /var/log/messages              # Follow system log
tail -f /var/log/secure                # Follow auth log
grep "error" /var/log/messages         # Search logs
grep -i "failed" /var/log/secure       # Case-insensitive search
dmesg | tail -20                       # Last kernel messages
cat /var/log/auth.log | grep ssh       # SSH auth attempts

# Log Rotation
cat /etc/logrotate.conf                # Log rotation config
logrotate -d /etc/logrotate.conf       # Test rotation (dry-run)
logrotate -f /etc/logrotate.conf       # Force rotation
```

---

## Quick Troubleshooting Scenarios

### Network Issues
```bash
# Check connectivity
ping 8.8.8.8                   # Test internet
traceroute google.com          # Trace route
mtr google.com                 # Continuous traceroute

# DNS Issues
nslookup example.com           # DNS lookup
dig example.com                # Detailed DNS
cat /etc/resolv.conf           # DNS config
systemctl restart systemd-resolved  # Restart DNS

# Network interfaces
ip addr show                   # All interfaces
ip link set eth0 up           # Bring up interface
ip link set eth0 down         # Bring down interface
```

### Disk Space Issues
```bash
# Find space hogs
du -sh /* | sort -hr          # Largest directories
find / -size +1G -type f      # Files over 1GB
du -sh /var/log/*             # Log directory sizes
df -h                         # Overall disk usage
```

### High CPU/Memory
```bash
# Find heavy processes
top -b -n 1 | head -20        # Top 20 CPU consumers
ps aux --sort=-%cpu | head    # Sorted by CPU
ps aux --sort=-%mem | head    # Sorted by memory
iotop                         # I/O heavy processes
```

### SSH Connection Issues
```bash
# Test connectivity
ssh -v user@host              # Verbose output
ssh -vv user@host             # Very verbose
telnet host 22                # Port connectivity
ss -tlnp | grep :22           # Check SSH listening

# Check SSH config
sudo sshd -t                   # Test SSH config
systemctl status ssh/sshd     # SSH service status
sudo systemctl restart sshd   # Restart SSH
```

---

**Tags:** `#cheatsheet` `#quick-reference` `#linux` `#networking` `#firewall` `#selinux` `#troubleshooting`
