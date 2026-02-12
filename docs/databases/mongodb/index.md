# MongoDB

MongoDB collections, documents, queries, and administration.

---

## Overview

MongoDB is a NoSQL document database that stores data in JSON-like documents with flexible schemas.

### Collections & Documents

**Insert a Document:**
```bash
db.collection.insertOne({ name: "John", age: 30 })
```

**Query Documents:**
```bash
db.collection.find({ name: "John" })
```

**Update a Document:**
```bash
db.collection.updateOne({ name: "John" }, { $set: { age: 31 } })
```

---

## Related Resources
- [Databases Overview](../index.md)
- [MySQL](../mysql/index.md)
- [PostgreSQL](../postgres/index.md)

---

**Tags:** `#mongodb` `#nosql` `#database`
