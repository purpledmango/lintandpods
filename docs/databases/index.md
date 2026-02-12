# Databases

Database administration, configuration, and management guides for relational and NoSQL databases.

## Quick Links

### MySQL
- [User Management](mysql/index.md)
- [Configuration](mysql/index.md)
- [Backup & Recovery](mysql/index.md)

### MongoDB
- [Collections & Documents](mongodb/index.md)
- [Queries](mongodb/index.md)
- [Indexing](mongodb/index.md)

### PostgreSQL
- [User Management](postgres/index.md)
- [Configuration](postgres/index.md)
- [Backup & Recovery](postgres/index.md)

---

## Common Administration Tasks

### Connect to Database
```bash
# MySQL
mysql -u root -p

# MongoDB
mongosh

# PostgreSQL
psql -U postgres
```

### Create User
```bash
# MySQL
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';

# PostgreSQL
CREATE USER username WITH PASSWORD 'password';
```

---

**Tags:** `#databases` `#mysql` `#mongodb` `#postgres`
