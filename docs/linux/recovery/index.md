# Recovery & Rescue

System recovery techniques including data rescue using rescue images and password reset with live ISO.

---

## Using Rescue Mode to Recover Data

### Boot into Rescue Mode

**Question: How do I boot into rescue mode to recover data?**

**Steps:**

1. **Reboot the system:**
```bash
sudo reboot
```

2. **During boot, enter GRUB menu:**
   - Press `Esc` or `Shift` while booting (timing varies)

3. **Select rescue kernel or edit boot parameters:**
   - Look for "Rescue" option in GRUB menu
   - Or edit boot parameters to add `rd.break` or `rescue`

4. **Boot parameters for different scenarios:**
```
# RHEL/CentOS/Fedora - Add to kernel line:
rd.break
enforcing=0

# Then press Ctrl+X to boot
```

**After booting into rescue mode:**
```bash
# You may be dropped to a shell automatically
# If not, select rescue mode from menu

# Switch to root
su -

# Mount the root filesystem (usually read-only)
mount -o remount,rw /

# Now you can access /sysroot or / depending on setup
ls -la /sysroot

# Or if filesystem is mounted at root:
ls -la /
```

**Tags:** `#rescue` `#recovery` `#boot` `#grub`

---

## Recovering Data from Rescue Image

### Copy Data from Damaged System

**Question: How do I recover data using a rescue image?**

**Scenario:** Your system won't boot, but you need to recover data from a disk.

**Steps:**

1. **Create/Download Rescue Image:**
   - Download RHEL ISO or use a Live Linux distribution
   - Burn to USB or mount as ISO in hypervisor

2. **Boot from Rescue Image:**
   - For physical server: Boot from USB
   - For VM: Attach ISO and boot from CD/DVD

3. **Once booted into rescue environment:**
```bash
# Wait for prompt (may need to press Enter)
# You should be in a minimal shell

# List available disks
lsblk
fdisk -l

# Mount the data partition (read-only recommended for recovery)
mkdir -p /mnt/recovery
mount -o ro /dev/sda2 /mnt/recovery  # Adjust device name

# Or mount without read-only if you need to copy/delete:
mount /dev/sda2 /mnt/recovery

# Verify mount
ls -la /mnt/recovery
```

4. **Copy Critical Data:**
```bash
# If you have external storage/USB mounted:
mount /dev/sdX1 /mnt/backup

# Copy data
cp -r /mnt/recovery/home/* /mnt/backup/
cp -r /mnt/recovery/opt/* /mnt/backup/
cp -r /mnt/recovery/var/www/* /mnt/backup/
```

5. **Alternatively, transfer over network:**
```bash
# In rescue environment
ifconfig  # Check if networking is available

# If network is available, use scp or rsync
# From your remote machine:
ssh root@<rescue-system-ip>
# Then from rescue shell:
scp -r /mnt/recovery/important_data user@remote-server:/backup/
```

6. **Unmount safely:**
```bash
umount /mnt/recovery
umount /mnt/backup
```

**Tags:** `#rescue` `#recovery` `#data-recovery` `#backup`

---

### Repair Filesystem Errors

**Question: How do I repair filesystem errors in rescue mode?**

**Command:**
```bash
# First, unmount the filesystem
umount /dev/sda2

# Check and repair (use -y for automatic repairs)
fsck -y /dev/sda2

# For ext4 specifically:
fsck.ext4 -y /dev/sda2

# For XFS:
xfs_repair /dev/sda2
```

**Warning:** Do NOT use `fsck` on mounted filesystems unless in read-only mode.

**Tags:** `#rescue` `#fsck` `#filesystem` `#repair`

---

## Password Reset Using Live ISO

### Reset Root Password with Live ISO

**Question: How do I reset the root password when locked out?**

**Steps:**

1. **Boot from Live ISO/USB:**
   - Insert USB or mount ISO in VM
   - Boot from it
   - Select "Live" or "Troubleshooting" option

2. **Once in Live environment:**
```bash
# Wait for desktop or shell to load
# You might get a desktop - open a terminal
# Or you might be in a shell already

# Check available disks
lsblk
```

3. **Mount the root filesystem:**
```bash
# Create mount point
mkdir -p /mnt/sysroot

# Mount root partition (find correct device first)
mount /dev/sda2 /mnt/sysroot

# Change root into mounted system
chroot /mnt/sysroot
```

4. **Reset the password:**
```bash
# Once inside chroot, reset password
passwd root

# Enter new password twice
New password: <enter password>
Retype new password: <enter password>
passwd: password updated successfully

# Exit chroot
exit
```

5. **Unmount and reboot:**
```bash
# Back in live environment
umount /mnt/sysroot
reboot
```

6. **Boot normally and login:**
```bash
# System will reboot
# Login as root with new password
```

**Tags:** `#password` `#reset` `#live-iso` `#grub` `#recovery`

---

### Alternative: Reset Password via GRUB

**Question: Can I reset password from GRUB bootloader?**

**Steps:**

1. **At GRUB menu, press 'e' to edit boot parameters**

2. **Find the line starting with `linux` or `kernel`**
   - It usually contains `/vmlinuz-...`

3. **Add `rd.break` to the end of the line:**
```
linux /vmlinuz-5.14.0-427.el9.x86_64 root=UUID=... rhgb quiet rd.break
```

4. **Press Ctrl+X to boot**

5. **Once dropped to emergency shell:**
```bash
# Remount root filesystem as read-write
mount -o remount,rw /sysroot

# Change to root directory
chroot /sysroot

# Reset password
passwd root

# Type new password

# Exit
exit

# Reboot
reboot
```

**Tags:** `#password` `#reset` `#grub` `#rd.break`

---

## Emergency Filesystem Access

### Access Filesystem Without Booting

**Question: How do I access a system's filesystem without booting the OS?**

**Scenario:** System is corrupted or you need to modify files before it boots.

**Steps:**

1. **Boot from Live USB/ISO**

2. **Mount the root partition:**
```bash
# Identify the partition
lsblk
fdisk -l

# Mount it
mkdir -p /mnt/root
mount /dev/sda2 /mnt/root
```

3. **Access and edit files:**
```bash
# View/edit config files
cat /mnt/root/etc/fstab
nano /mnt/root/etc/fstab

# Delete corrupted files
rm /mnt/root/path/to/bad/file

# Fix permissions
chmod 755 /mnt/root/etc/shadow
```

4. **Regenerate configurations if needed:**
```bash
chroot /mnt/root
# Run commands to rebuild configs
grub2-mkconfig -o /boot/grub2/grub.cfg
exit
```

5. **Unmount and reboot:**
```bash
umount /mnt/root
reboot
```

**Tags:** `#emergency` `#filesystem` `#access` `#recovery`

---

## Creating Rescue Media

### Create Rescue USB Drive

**Question: How do I create a bootable rescue USB?**

**From Linux:**
```bash
# Download ISO file first
# Then identify USB device
lsblk

# Unmount if already mounted
umount /dev/sdX1

# Write ISO to USB (be very careful with device name)
sudo dd if=rhel-9-x86_64-dvd.iso of=/dev/sdX bs=4M status=progress
sudo sync
```

**Using GNOME Disks (GUI):**
```bash
# Open "Disks" application
# Select USB drive
# Click menu → Restore Disk Image
# Select ISO file
# Click "Start Restoring"
```

**Tags:** `#rescue` `#usb` `#bootable` `#media`

---

## Related Resources
- [Disk Operations](../disk-operations/index.md)
- [Linux Overview](../index.md)
- [RHEL](../rhel/index.md)

---

**Tags:** `#recovery` `#rescue` `#password-reset` `#data-recovery` `#emergency`
