# Disk Operations

Disk management, partitioning, formatting, and storage configuration for Linux systems.

---

## Viewing Disk Information

### List All Disks and Partitions

**Question: How do I view all disks and their partitions?**

**Command:**
```bash
lsblk
```

**Expected Output:**
```
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   20G  0 disk
├─sda1   8:1    0    1G  0 part /boot
└─sda2   8:2    0   19G  0 part /
sdb      8:16   0  100G  0 disk
└─sdb1   8:17   0  100G  0 part /data
```

**Tags:** `#disk` `#lsblk` `#partitions`

---

### Display Disk Size and Usage

**Question: How do I check disk capacity and usage?**

**Command:**
```bash
df -h
```

**Expected Output:**
```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1       976M  200M  710M  23% /boot
/dev/sda2        19G  5.2G   13G  30% /
/dev/sdb1       100G  45G   55G  45% /data
tmpfs           2.0G     0  2.0G   0% /dev/shm
```

**Tags:** `#disk` `#df` `#storage` `#usage`

---

### Check Inode Usage

**Question: How do I check inode usage?**

**Command:**
```bash
df -i
```

**Note:** Inodes represent file count limit, not disk space. Running out of inodes prevents file creation even if space is available.

**Tags:** `#disk` `#inode` `#usage`

---

## Partitioning with fdisk

### Create a New Partition

**Question: How do I create a new partition on a disk?**

**Command:**
```bash
# List interactive fdisk menu
sudo fdisk /dev/sdb

# Then follow these steps in fdisk:
# 1. Press 'n' to create new partition
# 2. Select 'p' for primary or 'e' for extended
# 3. Enter partition number
# 4. Specify first and last sector (or size)
# 5. Press 'w' to write changes
```

**Example Session:**
```bash
sudo fdisk /dev/sdb
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-209715199, default 2048): 2048
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-209715199, default 209715199): +50G
Command (m for help): w
```

**Tags:** `#disk` `#fdisk` `#partition` `#partitioning`

---

## Formatting Partitions

### Format Partition with ext4

**Question: How do I format a partition with ext4 filesystem?**

**Command:**
```bash
# Warning: This will erase all data on the partition
sudo mkfs.ext4 /dev/sdb1
```

**With Custom Label:**
```bash
sudo mkfs.ext4 -L mydata /dev/sdb1
```

**Expected Output:**
```
mke2fs 1.45.6 (20-Mar-2022)
Creating filesystem with 26214144 4k blocks and 6553600 inodes
Filesystem UUID: a1b2c3d4-e5f6-47g8-h9i0-j1k2l3m4n5o6
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624, 11239424, 20480000, 23887872
Allocating group tables: done
Writing inode tables: done
Creating journal (131072 blocks): done
Writing superblocks and filesystem descriptor blocks: done
```

**Tags:** `#disk` `#format` `#ext4` `#mkfs`

---

### Format Partition with XFS

**Question: How do I format a partition with XFS filesystem?**

**Command:**
```bash
sudo mkfs.xfs /dev/sdb1
```

**With Custom Label:**
```bash
sudo mkfs.xfs -L mydata /dev/sdb1
```

**Tags:** `#disk` `#format` `#xfs` `#mkfs`

---

## Mounting Partitions

### Mount a Partition Temporarily

**Question: How do I mount a partition temporarily?**

**Command:**
```bash
# Create mount point
sudo mkdir -p /mnt/mydata

# Mount the partition
sudo mount /dev/sdb1 /mnt/mydata

# Verify mount
mount | grep sdb1
```

**Expected Output:**
```
/dev/sdb1 on /mnt/mydata type ext4 (rw,relatime)
```

**Tags:** `#disk` `#mount` `#mounting`

---

### Mount a Partition Permanently (Using /etc/fstab)

**Question: How do I mount a partition permanently?**

**Steps:**

1. **Get the UUID of the partition:**
```bash
sudo blkid /dev/sdb1
```

**Output:**
```
/dev/sdb1: UUID="a1b2c3d4-e5f6-47g8-h9i0-j1k2l3m4n5o6" TYPE="ext4"
```

2. **Edit /etc/fstab:**
```bash
sudo nano /etc/fstab
```

3. **Add the following line:**
```
UUID=a1b2c3d4-e5f6-47g8-h9i0-j1k2l3m4n5o6  /mnt/mydata  ext4  defaults,nofail  0  2
```

**Parameters:**
- `defaults` - Standard mount options
- `nofail` - System won't fail if this device is missing
- `0` - Dump frequency (0 = no dump)
- `2` - Filesystem check order (2 = after root)

4. **Mount without reboot:**
```bash
sudo mount -a
```

5. **Verify:**
```bash
mount | grep sdb1
df -h /mnt/mydata
```

**Tags:** `#disk` `#mount` `#fstab` `#permanent`

---

## Unmounting Partitions

### Unmount a Partition

**Question: How do I safely unmount a partition?**

**Command:**
```bash
# Unmount the partition
sudo umount /mnt/mydata

# Verify it's unmounted
mount | grep sdb1  # Should show nothing
```

**Tags:** `#disk` `#umount` `#unmount`

---

### Force Unmount (Use with Caution)

**Command:**
```bash
# Force unmount when files are in use
sudo umount -f /mnt/mydata

# Or lazy unmount (unmounts when no longer in use)
sudo umount -l /mnt/mydata
```

**Tags:** `#disk` `#umount` `#force`

---

## Disk Usage Analysis

### Find Large Files and Directories

**Question: How do I find what's using disk space?**

**Command:**
```bash
# Check directory sizes
du -sh /home/*

# Sort by size
du -sh /home/* | sort -h

# Find top 10 largest directories
du -sh /home/* | sort -rh | head -10
```

**Expected Output:**
```
4.2G    /home/user1
2.1G    /home/user2
1.5G    /home/user3
```

**Tags:** `#disk` `#du` `#usage` `#space`

---

### Find Large Individual Files

**Command:**
```bash
# Find files larger than 100MB
find /home -type f -size +100M -exec ls -lh {} \;

# More efficient version
find /home -type f -size +100M -ls
```

**Tags:** `#disk` `#find` `#files` `#size`

---

## Related Resources
- [Linux Overview](../index.md)
- [Recovery & Rescue](../recovery/index.md)
- [RHEL](../rhel/index.md)

---

**Tags:** `#linux` `#disk` `#storage` `#partitioning`
