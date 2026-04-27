# MongoDB Atlas + Compass + Colab Setup Guide

## Overview

This guide walks you through setting up a MongoDB cloud database using **MongoDB Atlas**, connecting it with **MongoDB Compass**, and interacting with it through **Google Colab** using Python (`pymongo`).

---

## 1. Create a MongoDB Atlas Deployment

1. Click **Create** (Free tier).
2. Choose a **name** for your project (make it memorable).
3. Select a **cloud provider** (e.g., AWS).
4. (Optional) Add a tag (e.g., `application: one`).
5. Click **Create Deployment**.

---

## 2. Set Up Database Access (Security)

1. Create a **username** and **password**.
2. Save these credentials securely.
3. Click **Create Database User**.

---

## 3. Choose Connection Method

1. Click **Choose a connection method**.
2. Select **MongoDB Compass** (for local GUI interaction).

---

## 4. Install MongoDB Compass

1. Choose **"I don’t have MongoDB Compass installed"**.
2. Select your operating system and download it.
3. **Important:** Keep the connection window open — you’ll need the connection URI.

---

## 5. Connect MongoDB Atlas to Compass

1. Open MongoDB Compass.
2. Click **+ Add new connection**.
3. Paste the connection URI from Atlas.
4. Replace `<db_password>` with your actual password.
5. Optionally:

   * Name your connection.
   * Choose a color.
6. Click **Save & Connect**.

---

## 6. Configure Network Access (Important)

If connection fails due to IP restrictions:

1. Go to **Network Access** in MongoDB Atlas.
2. Click **Add IP Address**.
3. Select:

   * `Allow Access from Anywhere (0.0.0.0/0)` *(for practice only)*.
4. Save changes.

⚠️ In production environments, restrict IP access properly.

---

## 7. Use MongoDB with Google Colab

### Install Dependencies

```python
!pip -q install --upgrade pymongo certifi
```

### Import Libraries

```python
from urllib.parse import quote_plus
from getpass import getpass
from pymongo import MongoClient
from pymongo.errors import ServerSelectionTimeoutError
import certifi
```

---

## 8. Secure Connection Setup

```python
# Secure credentials input
USER = input("Atlas Username: ").strip()
PASSWORD = quote_plus(getpass("Atlas Password: ").strip())
CLUSTER_HOST = input("Cluster host (e.g. cluster0.abcd123.mongodb.net): ").strip()

# MongoDB URI
URI = f"mongodb+srv://{USER}:{PASSWORD}@{CLUSTER_HOST}/?retryWrites=true&w=majority&appName=ClassDemo"

# Connection setup
client = MongoClient(
    URI,
    tls=True,
    tlsCAFile=certifi.where(),
    serverSelectionTimeoutMS=12000
)

try:
    client.admin.command("ping")
    print("Successfully connected to MongoDB Atlas")
except ServerSelectionTimeoutError:
    print("Connection timeout. Check IP whitelist or credentials.")
    raise
except Exception:
    print("TLS/SSL error. Verify configuration.")
    raise
```

---

## 9. Access Database and Collection

```python
db = client["school"]
students = db["students"]

print("Using database:", db.name)
print("Using collection:", students.name)
```

---

## Common Issues & Fixes

### 1. IP Not Allowed

* Add your IP or use `0.0.0.0/0` temporarily.

### 2. SSL Errors

* Update dependencies:

```python
!pip install --upgrade pymongo certifi
```

### 3. Authentication Errors

* Ensure:

  * Correct username/password
  * Password is URL-encoded (handled by `quote_plus`)

---

## Notes

* Never hardcode credentials in production.
* Always remove open IP access after testing.
* Use environment variables or secret managers for real projects.

---

## Goal of This Setup

* Learn MongoDB in different environments:

  * Cloud (Atlas)
  * Local GUI (Compass)
  * Code-based interaction (Colab + Python)

---

## Next Steps

* Insert data into collections
* Perform queries and aggregations
* Work with real datasets (e.g., students CSV)

---
