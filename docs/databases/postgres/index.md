# PostgreSQL

PostgreSQL user management, configuration, and administration.

---

## Overview

PostgreSQL is a powerful, open-source relational database system.

### User Management

**Connect to PostgreSQL:**
```bash
psql -U postgres
```

**Create a User:**
```sql
CREATE USER username WITH PASSWORD 'password';
```

**Grant Privileges:**
```sql
GRANT ALL PRIVILEGES ON DATABASE mydb TO username;
```

**List Users:**
```sql
\du
```

---

## Related Resources
- [Databases Overview](../index.md)
- [MySQL](../mysql/index.md)
- [MongoDB](../mongodb/index.md)

---

**Tags:** `#postgres` `#postgresql` `#database`
