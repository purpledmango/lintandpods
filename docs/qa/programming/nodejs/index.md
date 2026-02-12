# Node.js Development Q&A

Common Node.js development issues, errors, and solutions.

---

## Module Not Found

### Question: Why do I get "Cannot find module" error?

**Error:**
```
Error: Cannot find module 'module-name'
    at ...
```

**Causes & Solutions:**

```javascript
// Cause 1: Package not installed
// Solution: Install package
npm install module-name

// Cause 2: Typo in require
require('moduleName')  // Wrong case sensitivity
require('module-name')  // Correct

// Cause 3: Local module not found
require('./utils')  // Missing .js or /index.js
require('./utils/index.js')  // Correct

// Cause 4: node_modules not installed
npm install  // From project root

// Cause 5: Wrong path
require('../utils')  // Check relative path
```

**Diagnostic:**

```bash
# Check if package is installed
npm list module-name

# Check package.json
cat package.json | grep module-name

# Reinstall dependencies
rm -rf node_modules package-lock.json
npm install
```

**Tags:** `#nodejs` `#modules` `#troubleshooting`

---

## Port Already in Use

### Question: How do I fix "Port already in use" error?

**Error:**
```
Error: listen EADDRINUSE: address already in use :::3000
```

**Solutions:**

```bash
# Find process using port
lsof -i :3000
netstat -tlnp | grep 3000

# Kill the process
kill -9 PID

# Or use different port
PORT=3001 npm start

# For macOS
lsof -i :3000 | grep -v PID | awk '{print $2}' | xargs kill -9
```

**Node.js Code Solution:**

```javascript
// In Node.js code
const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
  
  // Handle error if port in use
  .on('error', (err) => {
    if (err.code === 'EADDRINUSE') {
      console.error(`Port ${PORT} already in use`);
      process.exit(1);
    }
  });
});
```

**Tags:** `#nodejs` `#port` `#troubleshooting`

---

## Database Connection Failed

### Question: How do I troubleshoot database connection issues?

**Symptoms:**
- "Connection refused"
- "ECONNREFUSED"
- "Connection timeout"

**Diagnostic:**

```bash
# Test database connectivity
telnet localhost 5432
mysql -h localhost -u user -p

# Check database logs
tail -f /var/log/mysql/error.log
journalctl -u postgres -f
```

**Node.js Solutions:**

```javascript
// Wrong credentials
const mysql = require('mysql');
const connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',  // Wrong user
  password: 'password',
  database: 'mydb'
});

// Correct with error handling
const pool = mysql.createPool({
  host: process.env.DB_HOST || 'localhost',
  user: process.env.DB_USER || 'user',
  password: process.env.DB_PASSWORD,
  database: process.env.DB_NAME,
  waitForConnections: true,
  connectionLimit: 10,
  queueLimit: 0
});

pool.on('error', (err) => {
  console.error('Database error:', err.code);
  if (err.code === 'ECONNREFUSED') {
    console.error('Database server not running');
  }
});
```

**Tags:** `#nodejs` `#database` `#troubleshooting`

---

## Memory Leak

### Question: How do I find and fix memory leaks?

**Diagnostic:**

```bash
# Monitor memory usage
node --expose-gc app.js

# Check growing memory
watch -n 1 'ps aux | grep node'
```

**Node.js Tools:**

```javascript
// Using heapdump
const heapdump = require('heapdump');

// Take snapshot
heapdump.writeSnapshot('./heap-${Date.now()}.heapsnapshot');

// Or use clinic.js
// npm install -g clinic
// clinic doctor -- node app.js
```

**Common Causes:**

```javascript
// Cause 1: Event listeners not removed
function bad() {
  emitter.on('data', listener);  // Added every call
}

// Fixed
function good() {
  emitter.once('data', listener);  // Only once
  // Or remove listener
  emitter.removeListener('data', listener);
}

// Cause 2: Circular references
let obj = { data: Array(1000000) };
obj.self = obj;  // Circular reference

// Cause 3: Timers not cleared
const timer = setInterval(() => {
  // Not cleared
}, 1000);

// Fixed
clearInterval(timer);
```

**Tags:** `#nodejs` `#memory` `#performance`

---

## Callback Hell / Unhandled Promises

### Question: How do I handle async/await and promise errors?

**Problem:**

```javascript
// Callback hell
fs.readFile('file.txt', (err, data) => {
  if (err) console.error(err);
  db.query('SELECT ...', (err, result) => {
    if (err) console.error(err);
    // Nested callbacks...
  });
});

// Unhandled promise rejection
function getData() {
  return fetch('/api/data')
    .then(r => r.json())  // Error not caught
    .catch(err => console.log(err));
}
```

**Solutions:**

```javascript
// Use async/await
async function processData() {
  try {
    const data = await fs.promises.readFile('file.txt', 'utf-8');
    const result = await db.query('SELECT ...');
    return result;
  } catch (err) {
    console.error('Error:', err);
  }
}

// Handle unhandled rejections
process.on('unhandledRejection', (reason, promise) => {
  console.error('Unhandled Rejection at:', promise, 'reason:', reason);
  // Log to error tracking service
});

// Better error handling
async function getData() {
  try {
    const response = await fetch('/api/data');
    if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
    return await response.json();
  } catch (err) {
    console.error('Failed to fetch data:', err);
    throw err;  // Re-throw if needed
  }
}
```

**Tags:** `#nodejs` `#async` `#promises`

---

## High CPU / Slow Performance

### Question: How do I profile and optimize Node.js performance?

**Profiling:**

```bash
# CPU profiling
node --prof app.js
node --prof-process isolate-*.log > processed.txt

# Using clinic.js
clinic doctor -- node app.js
clinic flame -- node app.js

# Native Node profiler
node --inspect app.js
# Then use Chrome DevTools: chrome://inspect
```

**Common Performance Issues:**

```javascript
// Issue 1: Blocking operations
// WRONG - Blocks event loop
const data = fs.readFileSync('large-file.txt');

// RIGHT - Async
const data = await fs.promises.readFile('large-file.txt');

// Issue 2: Inefficient loops
// WRONG - O(n²)
for (let i = 0; i < arr.length; i++) {
  for (let j = 0; j < arr.length; j++) {
    // Do something
  }
}

// RIGHT - Use Set or Map
const cache = new Set(arr);
if (cache.has(item)) { /* ... */ }

// Issue 3: Memory-intensive operations
// WRONG - Store all data
const results = [];
for (let i = 0; i < 1000000; i++) {
  results.push(heavyComputation(i));
}

// RIGHT - Stream or paginate
function* generate() {
  for (let i = 0; i < 1000000; i++) {
    yield heavyComputation(i);
  }
}
```

**Tags:** `#nodejs` `#performance` `#profiling`

---

## Dependency Conflicts

### Question: How do I resolve npm dependency conflicts?

**Issues:**

```bash
# Dependency version conflict
npm ERR! could not resolve dependency

# Security vulnerabilities
npm audit
npm audit fix

# Duplicate packages
npm ls  # See dependency tree
```

**Solutions:**

```bash
# Check dependencies
npm list
npm ls | grep module-name

# Update dependencies safely
npm update
npm update module-name@latest

# Use npm audit
npm audit  # See vulnerabilities
npm audit fix  # Auto fix
npm audit fix --force  # Force update

# Clear cache
npm cache clean --force

# Reinstall clean
rm -rf node_modules package-lock.json
npm install
```

**Tags:** `#nodejs` `#npm` `#dependencies`

---

**Tags:** `#nodejs` `#development` `#qa` `#troubleshooting`
