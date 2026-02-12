# Database Issues & Troubleshooting

MySQL, PostgreSQL, MongoDB, and database connection problems.

---

## Cannot Connect to Database

### Question: Why can't I connect to the database?

**Symptoms:**
- "Connection refused"
- "Connection timeout"
- "Access denied for user"

**Diagnostic Steps:**

```bash
# Test connectivity
mysql -h 192.168.1.100 -u user -p
psql -h host -U user -d database
mongo --host host:port

# Check if service is running
systemctl status mysql
systemctl status postgresql

# Check listening ports
ss -tuln | grep 3306

# Verify firewall
firewall-cmd --list-ports
```

**Common Causes & Solutions:**

| Cause | Check | Fix |
|-------|-------|-----|
| Service not running | `systemctl status mysql` | `systemctl start mysql` |
| Wrong credentials | User/password | Verify in DB, reset if needed |
| Access denied by host | MySQL grants | `GRANT ALL ON *.* TO user@host` |
| Connection refused | Port/firewall | Open port, check bind address |
| Wrong host/port | Connection string | Verify hostname and port |

**Tags:** `#database` `#troubleshooting` `#connection`

---

## Slow Query Performance

### Question: How do I diagnose and optimize slow queries?

**MySQL:**

```bash
# Enable query log
mysql> SET GLOBAL slow_query_log = 'ON';
mysql> SET GLOBAL long_query_time = 2;

# Find slow queries
tail -f /var/log/mysql/slow.log

# Analyze query
EXPLAIN SELECT ...;
EXPLAIN FORMAT=JSON SELECT ...;

# Show running queries
SHOW PROCESSLIST;
SHOW FULL PROCESSLIST;
```

**Common Solutions:**

```bash
# Add index
CREATE INDEX idx_name ON table(column);

# Analyze statistics
ANALYZE TABLE table_name;

# Check query plan
EXPLAIN EXTENDED SELECT ...;
```

**PostgreSQL:**

```bash
# Enable slow query logging
ALTER SYSTEM SET log_statement = 'all';
ALTER SYSTEM SET log_min_duration_statement = 1000;
SELECT pg_reload_conf();

# Check query plan
EXPLAIN SELECT ...;
EXPLAIN ANALYZE SELECT ...;
```

**Tags:** `#database` `#performance` `#troubleshooting`

---

## Database Replication Lag

### Question: How do I check and fix replication lag?

**MySQL:**

```bash
# Check slave status
SHOW SLAVE STATUS\G

# Key fields:
# - Seconds_Behind_Master (lag in seconds)
# - Relay_Master_Log_File
# - Exec_Master_Log_Pos

# View binary log
SHOW MASTER STATUS;
SHOW BINARY LOGS;

# Check binary log position
SHOW SLAVE STATUS\G | grep "Exec_Master_Log_Pos"
```

**Solutions:**

```bash
# Parallel replication threads
SET GLOBAL slave_parallel_workers = 4;
SET GLOBAL slave_parallel_type = 'LOGICAL_CLOCK';

# Skip problematic events if needed
SET GLOBAL SQL_SLAVE_SKIP_COUNTER = 1;
START SLAVE;
```

**Tags:** `#database` `#replication` `#troubleshooting`

---

## Disk Space Full

### Question: Database running out of disk space

**Solutions:**

```bash
# Check current size
du -sh /var/lib/mysql
du -sh /var/lib/postgresql

# Clean up old logs
mysql> PURGE BINARY LOGS BEFORE '2024-01-01';

# Archive old data
SELECT * INTO OUTFILE '/tmp/old_data.sql' FROM table WHERE date < '2024-01-01';
DELETE FROM table WHERE date < '2024-01-01';

# Check what's using space
find /var/lib/mysql -type f -size +100M

# Extend volume if using LVM
lvextend -L +50G /dev/vg0/lv_data
resize2fs /dev/vg0/lv_data
```

**Tags:** `#database` `#storage` `#troubleshooting`

---

## Backup / Restore Failed

### Question: How do I troubleshoot backup/restore issues?

**MySQL Backup:**

```bash
# Backup
mysqldump -u user -p database > backup.sql

# Check for errors
mysqldump -u user -p database 2>&1 | tail -20

# Large databases
mysqldump --quick --lock-tables=false database | gzip > backup.sql.gz
```

**Restore:**

```bash
# From backup
mysql -u user -p database < backup.sql

# Check for errors
mysql -u user -p database < backup.sql 2>&1 | tail -20

# From compressed backup
gunzip < backup.sql.gz | mysql -u user -p database
```

**Common Issues:**

| Issue | Solution |
|-------|----------|
| Backup file corrupted | Use file recovery tools |
| Restore hangs | Check available disk space |
| Permission denied | Check user privileges |
| Table lock timeout | Use --lock-tables=false |

**Tags:** `#database` `#backup` `#troubleshooting`

---

**Tags:** `#troubleshooting` `#database` `#mysql` `#postgresql` `#mongodb`
