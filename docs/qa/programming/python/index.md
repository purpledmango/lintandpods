# Python Development Q&A

Common Python development issues, errors, and solutions.

---

## ImportError / ModuleNotFoundError

### Question: Why can't Python find my module?

**Error:**
```
ModuleNotFoundError: No module named 'module_name'
ImportError: cannot import name 'something'
```

**Causes & Solutions:**

```python
# Cause 1: Package not installed
# Solution: Install package
# pip install module-name

# Cause 2: Wrong module name
from json import load  # Correct
from JSON import load  # Wrong - Python is case-sensitive

# Cause 3: Wrong import path
import sys
sys.path.insert(0, '/path/to/modules')  # Add to path
from module_name import function

# Cause 4: Relative imports
# In package/submodule.py
from .utils import helper  # Relative import
from package.utils import helper  # Absolute import

# Cause 5: Virtual environment not activated
# Solution: source venv/bin/activate
```

**Diagnostic:**

```bash
# Check if package is installed
pip list | grep module-name
python -c "import module_name; print(module_name.__file__)"

# Check Python path
python -c "import sys; print(sys.path)"

# Show import search paths
python -c "import site; print(site.getsitepackages())"
```

**Tags:** `#python` `#imports` `#troubleshooting`

---

## Virtual Environment Issues

### Question: How do I fix virtual environment problems?

**Problems & Solutions:**

```bash
# Create virtual environment
python -m venv venv
python3 -m venv venv

# Activate
source venv/bin/activate        # Linux/Mac
venv\Scripts\activate           # Windows

# Check Python version
which python
python --version

# Wrong Python version - recreate venv
rm -rf venv
python3.10 -m venv venv
source venv/bin/activate

# Broken venv - reinstall packages
pip install --upgrade pip setuptools wheel
pip install -r requirements.txt

# List installed packages
pip list
pip freeze > requirements.txt
```

**Tags:** `#python` `#venv` `#environment`

---

## pip Install Issues

### Question: How do I fix pip installation problems?

**Problems:**

```bash
# Error: Could not find a version
pip install package-name==1.0.0  # Wrong version

# Solution: Check available versions
pip index versions package-name
pip search package-name  # May not work

# Upgrade pip
pip install --upgrade pip

# Clear cache
pip cache purge

# Install from requirements
pip install -r requirements.txt --verbose

# Network issues
pip install --retries 5 package-name

# Behind proxy
pip install --proxy proxy_url package-name
```

**Tags:** `#python` `#pip` `#packages`

---

## Database Connection Failed

### Question: How do I troubleshoot database connections?

**Solutions:**

```python
import psycopg2
import mysql.connector

# PostgreSQL
try:
    conn = psycopg2.connect(
        host="localhost",
        user="username",
        password="password",
        database="mydb"
    )
except psycopg2.OperationalError as e:
    print(f"Connection failed: {e}")

# MySQL
try:
    conn = mysql.connector.connect(
        host="localhost",
        user="user",
        password="password",
        database="mydb"
    )
except mysql.connector.Error as e:
    if e.errno == 1045:
        print("Invalid username or password")
    elif e.errno == 2003:
        print("Cannot connect to host")
```

**Debugging:**

```bash
# Test database connectivity
telnet localhost 5432
mysql -h localhost -u user -p
```

**Tags:** `#python` `#database` `#troubleshooting`

---

## Memory Leak / High Memory Usage

### Question: How do I find and fix memory leaks?

**Profiling:**

```python
import tracemalloc
import sys

# Simple memory tracking
tracemalloc.start()

# ... your code ...

current, peak = tracemalloc.get_traced_memory()
print(f"Current: {current / 1024 / 1024:.2f} MB")
print(f"Peak: {peak / 1024 / 1024:.2f} MB")

# Get top memory consumers
snapshot = tracemalloc.take_snapshot()
top_stats = snapshot.statistics('lineno')
for stat in top_stats[:3]:
    print(stat)

# Detailed memory profiling
# pip install memory_profiler
# python -m memory_profiler script.py
```

**Common Issues:**

```python
# Issue 1: Large lists in memory
# WRONG - Loads entire file
data = []
with open('large.txt') as f:
    for line in f:
        data.append(line)

# RIGHT - Generator, loads as needed
def read_file(filename):
    with open(filename) as f:
        for line in f:
            yield line

# Issue 2: Circular references with __del__
class Node:
    def __init__(self):
        self.parent = None
        self.children = []
    
    def __del__(self):
        pass  # Avoid if possible - can prevent garbage collection

# Issue 3: Global variables accumulating data
cache = {}  # Growing indefinitely

# Fixed with size limit
from functools import lru_cache

@lru_cache(maxsize=128)
def expensive_function(x):
    return x ** 2
```

**Tags:** `#python` `#memory` `#performance`

---

## Slow Performance / Timeout

### Question: How do I optimize Python performance?

**Profiling:**

```python
import cProfile
import pstats

# Profile function
cProfile.run('function_to_profile()')

# More detailed
pr = cProfile.Profile()
pr.enable()

# ... your code ...

pr.disable()
stats = pstats.Stats(pr)
stats.sort_stats('cumulative')
stats.print_stats(10)
```

**Optimization Techniques:**

```python
# Issue 1: Inefficient loops
# WRONG - O(n²)
for item in large_list:
    if item in another_large_list:
        pass

# RIGHT - Use set lookup O(1)
another_set = set(another_large_list)
for item in large_list:
    if item in another_set:
        pass

# Issue 2: String concatenation in loop
# WRONG - Creates new string each time
result = ""
for char in chars:
    result += char

# RIGHT - Use join
result = "".join(chars)

# Issue 3: List comprehension vs loops
# FAST - List comprehension
result = [x * 2 for x in range(1000000)]

# SLOW - Loop
result = []
for x in range(1000000):
    result.append(x * 2)

# Issue 4: NumPy for numerical operations
import numpy as np

# SLOW - Python loops
result = [x * 2 for x in range(1000000)]

# FAST - NumPy vectorized
arr = np.arange(1000000)
result = arr * 2
```

**Tags:** `#python` `#performance` `#optimization`

---

## Exception Handling

### Question: How do I properly handle exceptions?

**Problems:**

```python
# WRONG - Too broad
try:
    data = risky_operation()
except:
    print("Error occurred")

# RIGHT - Specific exception
try:
    data = risky_operation()
except FileNotFoundError as e:
    print(f"File not found: {e}")
except ValueError as e:
    print(f"Invalid value: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
finally:
    cleanup()
```

**Best Practices:**

```python
# Use context managers
with open('file.txt') as f:
    data = f.read()
# File automatically closed

# Custom exception
class CustomError(Exception):
    pass

try:
    if not condition:
        raise CustomError("Something went wrong")
except CustomError as e:
    print(f"Custom error: {e}")

# Logging errors
import logging

logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

try:
    risky_operation()
except Exception as e:
    logger.exception("Operation failed")  # Logs full traceback
```

**Tags:** `#python` `#exceptions` `#error-handling`

---

## Async/Await Issues

### Question: How do I debug async code issues?

**Problems:**

```python
import asyncio

# WRONG - Coroutine not awaited
async def fetch_data():
    result = fetch()  # Missing await
    return result

# RIGHT - Proper await
async def fetch_data():
    result = await fetch()
    return result

# Running async functions
# WRONG - Function returns coroutine
result = fetch_data()

# RIGHT - Use run or await
result = asyncio.run(fetch_data())

# Or in async context
async def main():
    result = await fetch_data()
```

**Debugging:**

```python
import asyncio
import logging

logging.basicConfig(level=logging.DEBUG)

# Debug event loop
asyncio.run(main(), debug=True)

# Check for unawaited coroutines
import warnings
warnings.filterwarnings('error', category=RuntimeWarning)

# Find long-running tasks
asyncio.create_task(long_task()).set_name('LongTask')
```

**Tags:** `#python` `#async` `#asyncio`

---

**Tags:** `#python` `#development` `#qa` `#troubleshooting`
