# Security & Access Issues

Common security, permissions, SELinux, SSH, and firewall problems.

---

## Permission Denied (Detailed)

See: [System Errors - Permission Denied](../troubleshooting/system-errors.md#permission-denied-errors)

---

## SELinux Denials

### Question: How do I troubleshoot SELinux denials?

**Symptoms:**
- Operation forbidden, no apparent permission issue
- Application cannot access files or bind ports

**Diagnostic:**

```bash
# Check SELinux status
getenforce
getsebool -a

# View denials
ausearch -m avc -ts recent

# Explain denials
tail -f /var/log/audit/audit.log | audit2why

# Get policy suggestions
ausearch -m avc | audit2allow -a -M custompolicy
semodule -i custompolicy.pp
```

**Common Fixes:**

```bash
# Restore file contexts
restorecon -R /path/to/directory

# Allow specific action
setsebool -P httpd_can_sendmail on

# Check port assignments
semanage port -l | grep 8080
semanage port -a -t http_port_t -p tcp 8080
```

**Tags:** `#security` `#selinux` `#troubleshooting`

---

## SSH Connection Issues

### Question: Why can't I SSH to a host?

**Diagnostic:**

```bash
# Test connectivity
ssh -v user@host
ssh -vvv user@host  # Very verbose

# Check SSH service
systemctl status ssh
sudo systemctl status sshd

# Verify SSH listening
ss -tlnp | grep 22

# Check keys
ls -la ~/.ssh/
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```

**Common Issues & Solutions:**

| Issue | Solution |
|-------|----------|
| Permission denied (password) | Check credentials, SSH config |
| Permission denied (public key) | Check authorized_keys permissions (644) |
| Connection refused | SSH service not running or wrong port |
| Too many authentication failures | Reset SSH config or add key |
| Host key verification failed | Accept key or remove from known_hosts |

**Fix:**

```bash
# Remove known_hosts entry
ssh-keygen -R hostname

# Add key to agent
ssh-add ~/.ssh/id_rsa

# Test key auth
ssh -i ~/.ssh/id_rsa user@host
```

**Tags:** `#security` `#ssh` `#troubleshooting`

---

## Firewall Blocking Traffic

### Question: How do I check if firewall is blocking connections?

**Solutions:**

```bash
# Check firewall status
sudo firewall-cmd --state
sudo systemctl status firewalld

# List all rules
sudo firewall-cmd --list-all

# Test port
telnet host port
nc -zv host port

# Temporarily disable firewall (testing only)
sudo systemctl stop firewalld

# Add rule
sudo firewall-cmd --add-port=8080/tcp
sudo firewall-cmd --add-service=http

# Make permanent
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```

**Tags:** `#security` `#firewall` `#troubleshooting`

---

## User Account Locked

### Question: How do I unlock a locked user account?

**Solutions:**

```bash
# Check if account locked
grep username /etc/shadow

# Unlock account
sudo passwd -u username
sudo usermod -U username

# Reset password
sudo passwd username

# Set expiration to never
sudo chage -E -1 username

# Check password status
sudo chage -l username
```

**Tags:** `#security` `#users` `#authentication`

---

**Tags:** `#security` `#permissions` `#troubleshooting`
