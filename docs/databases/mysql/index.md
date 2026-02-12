# MySQL

MySQL/MariaDB user management, configuration, and administration.

---

## User Management

### List All MySQL Users

**Question: How do I list all MySQL users and their hosts?**

**Command:**
```bash
# Connect to MySQL
mysql -u root -p
```

```sql
SELECT User, Host FROM mysql.user;
```

**Expected Output:**
```
| User                | Host      |
| root                | localhost |
| mysql.infoschema    | localhost |
| mysql.session       | localhost |
| mysql.sys           | localhost |
```

**Tags:** `#mysql` `#users` `#database`

---

## Related Resources
- [Databases Overview](../index.md)
- [PostgreSQL](../postgres/index.md)
- [MongoDB](../mongodb/index.md)

---

**Tags:** `#mysql` `#database` `#user-management`
