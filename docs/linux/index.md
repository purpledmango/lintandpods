# Linux System Administration Guide

Comprehensive reference for Linux system administration, networking, storage management, and troubleshooting.

---

## 📚 Core Documentation

### Networking
- [Networking Basics](../networking/basics/osi-model.md) - OSI Model, IP Addressing, Subnetting fundamentals
- [Network Commands](../networking/commands/ip-command.md) - ip, nmcli, firewall-cmd, tcpdump utilities
- [Network Configuration](../rhel/configuration/network-config.md) - Static IP, DHCP, DNS, gateway setup
- [Network Troubleshooting](../networking/troubleshooting/connectivity.md) - Connectivity and DNS issues

### Storage & Disk Management
- [Disk Operations](disk-operations/index.md) - Partitioning, formatting, mounting filesystems
- [System Recovery](recovery/index.md) - GRUB recovery, boot issues, data recovery, password reset

### RHEL Configuration
- [Network Configuration](../rhel/configuration/network-config.md) - NetworkManager setup
- [Firewall Management](../rhel/configuration/firewall.md) - firewalld zones and rules
- [SELinux](../rhel/configuration/selinux.md) - SELinux modes and policy management
- [System Services](../rhel/services/ssh.md) - SSH, Apache (httpd), DNS services

---

## 🔥 Quick Command Reference

### System Information
```bash
uname -a                          # Full system info
hostnamectl status               # Hostname and OS
cat /etc/os-release             # OS details
uptime                          # System uptime
lsb_release -a                  # Distribution info
```

### Networking Configuration
```bash
# View network status
nmcli device status
nmcli connection show
ip addr show
ip route show

# Configure static IP
sudo nmcli connection modify ens33 ipv4.method manual
sudo nmcli connection modify ens33 ipv4.addresses "192.168.1.100/24"
sudo nmcli connection modify ens33 ipv4.gateway "192.168.1.1"
sudo nmcli connection modify ens33 ipv4.dns "8.8.8.8"
sudo nmcli connection up ens33

# Restart networking
sudo systemctl restart NetworkManager
```

### DNS Troubleshooting
```bash
nslookup example.com            # DNS lookup
dig example.com                 # Detailed DNS query
host example.com                # Simple DNS resolution
cat /etc/resolv.conf            # DNS configuration
ping -c 4 8.8.8.8              # Test connectivity
```

### Firewall Management
```bash
# Status and listing
sudo firewall-cmd --state
sudo firewall-cmd --list-all
sudo firewall-cmd --list-ports
sudo firewall-cmd --list-services

# Add/remove ports and services
sudo firewall-cmd --add-port=8080/tcp
sudo firewall-cmd --remove-port=8080/tcp
sudo firewall-cmd --add-service=http
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload
```

### Disk & Storage
```bash
# View disk usage
df -h                           # Filesystem space usage
du -sh /path                    # Directory size
lsblk                          # Block devices tree
fdisk -l                       # Partition information

# Disk operations
sudo mkfs.ext4 /dev/sda1       # Format partition
sudo mkdir /mnt/data           # Create mount point
sudo mount /dev/sda1 /mnt/data # Mount partition
sudo umount /mnt/data          # Unmount partition
sudo mount -o remount,rw /     # Remount as read-write

# Disk I/O monitoring
iostat -x 1 5                  # Detailed I/O stats
iotop                          # I/O per process
hdparm -i /dev/sda            # Drive information
```

### Process Management
```bash
# View processes
ps aux                          # All running processes
ps aux | grep process_name      # Find specific process
pgrep process_name             # Get PID
top                            # Real-time monitoring
htop                           # Interactive monitoring

# Kill processes
kill -15 PID                   # Graceful kill
kill -9 PID                    # Force kill
pkill process_name             # Kill by name

# Process priority
nice -n 10 command             # Run with lower priority
renice -n 5 -p PID             # Change process priority
```

### User & Permission Management
```bash
# User operations
useradd -m -s /bin/bash user   # Create user
userdel -r user                # Delete user
passwd user                    # Change password
usermod -aG wheel user         # Add to sudo group
id user                        # User information

# Permissions
chmod 755 file                 # Owner rwx, others rx
chmod 644 file                 # Owner rw, others r
chown user:group file          # Change ownership
chown -R user:group /dir       # Recursive change

# ACLs
getfacl /path                  # View ACLs
setfacl -m u:user:rwx /path   # Set ACL
```

### File Operations
```bash
# Finding files
find / -name "*.log" -type f   # Find files by name
find / -type f -mtime -7       # Modified in last 7 days
find / -size +100M             # Files larger than 100MB
locate file.txt                # Fast search (requires updatedb)

# Compression
tar -czf archive.tar.gz /path  # Create compressed tar
tar -xzf archive.tar.gz        # Extract tar.gz
zip -r archive.zip /path       # Create zip
unzip archive.zip              # Extract zip
```

### Log Management
```bash
# Systemd logs
journalctl                     # View all logs
journalctl -n 50              # Last 50 lines
journalctl -f                 # Follow logs in real-time
journalctl -u service_name     # Service-specific logs
journalctl --since "1 hour ago"  # Logs from last hour

# Traditional logs
tail -f /var/log/messages     # Follow system log
tail -f /var/log/secure       # Follow auth log
grep "error" /var/log/messages # Search logs
```

### Package Management
```bash
# DNF/YUM operations
sudo dnf search package_name   # Search package
sudo dnf install package_name  # Install package
sudo dnf remove package_name   # Remove package
sudo dnf update               # Update all packages
sudo dnf list installed       # List installed packages

# RPM operations
rpm -qa                       # List installed packages
rpm -qi package_name          # Package information
rpm -ql package_name          # List package files
rpm -V package_name           # Verify package
rpm -Fvh package.rpm          # Install/update RPM
```

### System Services
```bash
# Service management
systemctl status service_name   # Service status
sudo systemctl start service_name   # Start service
sudo systemctl stop service_name    # Stop service
sudo systemctl restart service_name # Restart service
sudo systemctl enable service_name  # Enable at boot
sudo systemctl disable service_name # Disable at boot
systemctl list-units --type=service # List all services
```

### SSH & Remote Access
```bash
# SSH connections
ssh user@host                  # Connect to host
ssh -p 2222 user@host         # Connect on custom port
ssh -i /path/key user@host    # Use specific key

# Key management
ssh-keygen -t rsa -b 4096     # Generate key pair
ssh-copy-id -i key.pub user@host  # Copy SSH key
chmod 600 ~/.ssh/id_rsa       # Set key permissions

# File transfer
scp file.txt user@host:/tmp/  # Copy to remote
scp user@host:/tmp/file.txt . # Copy from remote
sftp user@host                # SFTP session
```

### Performance & Monitoring
```bash
# CPU and memory
top -u username               # Monitor user processes
free -h                       # Memory usage
vmstat 1 5                    # Virtual memory stats
sar -u 1 5                    # CPU utilization

# Disk and I/O
iostat -x 1 5                # I/O statistics
iotop                        # I/O per process
df -h                        # Disk space

# Network
ss -tuln                     # Listening ports
netstat -an | grep ESTABLISHED  # Active connections
iftop                        # Network traffic per interface
```

### SELinux & Security
```bash
# SELinux status
getenforce                   # Current SELinux mode
setenforce Permissive        # Set to Permissive
getsebool -a                 # Boolean policies
setsebool -P boolean_name on # Set persistent boolean

# Troubleshooting
semanage port -l             # Allowed ports
restorecon -v /path          # Restore file context
audit2why < /var/log/audit/audit.log  # Explain denials
```

### Text Processing
```bash
# Searching and filtering
grep "pattern" file.txt       # Search pattern
grep -r "pattern" /path      # Recursive search
grep -i "pattern" file.txt   # Case-insensitive

# Text manipulation
sed 's/old/new/g' file.txt   # Replace text
awk '{print $1}' file.txt    # Extract fields
cut -d: -f1 /etc/passwd      # Column extraction

# Viewing
cat file.txt                  # Show file content
less file.txt                 # Paginated view
head -n 20 file.txt          # First 20 lines
tail -n 20 file.txt          # Last 20 lines
wc -l file.txt               # Line count
```

### Kernel & Modules
```bash
# Kernel information
uname -r                     # Kernel version
cat /proc/version            # Kernel details
dmesg | tail -20             # Last kernel messages
cat /proc/cpuinfo            # CPU information

# Kernel modules
lsmod                        # Loaded modules
modinfo module_name          # Module information
sudo modprobe module_name    # Load module
sudo modprobe -r module_name # Remove module
```

---

## 🎯 Common Scenarios

### Change Hostname
```bash
sudo hostnamectl set-hostname new-hostname
sudo systemctl restart systemd-hostnamed
```

### Add User to Sudoers
```bash
sudo usermod -aG wheel username    # RHEL/CentOS
sudo usermod -aG sudo username     # Debian/Ubuntu
```

### Mount Additional Drive
```bash
sudo lsblk                         # Find device
sudo mkdir /mnt/newdrive           # Create mount point
sudo mount /dev/sdb1 /mnt/newdrive # Mount
sudo blkid /dev/sdb1              # Get UUID
# Add to /etc/fstab for permanent mount
```

### Increase Disk Size (LVM)
```bash
sudo lvextend -L +10G /dev/vg0/lv0    # Extend LV
sudo resize2fs /dev/vg0/lv0           # Resize filesystem
df -h                                  # Verify
```

### Reset Root Password (RHEL)
```bash
# Boot into single-user or emergency mode
# Mount filesystem as read-write
mount -o remount,rw /
# Reset password
passwd
# Reboot
reboot
```

### Enable IP Forwarding
```bash
sudo sysctl -w net.ipv4.ip_forward=1           # Temporary
echo "net.ipv4.ip_forward=1" | sudo tee /etc/sysctl.conf  # Permanent
sudo sysctl -p                                  # Apply changes
```

---

## 📖 Learning Path

1. **Fundamentals**: Review system information and basic commands
2. **Networking**: Configure network interfaces and DNS
3. **Storage**: Manage disks, partitions, and mounts
4. **Users & Security**: Create users, manage permissions, configure firewall
5. **Services**: Manage systemd services and daemons
6. **Monitoring**: Monitor performance and troubleshoot issues
7. **Advanced**: SELinux, kernel tuning, disaster recovery

---

## 🔗 Additional Resources

- [Networking Documentation](../networking/index.md)
- [RHEL Configuration](../rhel/index.md)


---

**Tags:** `#linux` `#sysadmin` `#networking` `#storage` `#security` `#troubleshooting`
