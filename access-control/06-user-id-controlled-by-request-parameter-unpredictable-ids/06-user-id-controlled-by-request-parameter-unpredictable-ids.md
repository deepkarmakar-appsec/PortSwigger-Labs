# 🔐 PortSwigger Lab Writeup

## User ID Controlled by Request Parameter (Unpredictable User IDs)

---

## 📌 Overview

This lab demonstrates a **Horizontal Privilege Escalation vulnerability** caused by improper access control on user account endpoints.

Although the application uses **GUIDs (Globally Unique Identifiers)** to identify users, it fails to enforce proper **authorization checks**, allowing attackers to access other users' sensitive data.

---

## 🧠 Vulnerability Details

* **Type:** Broken Access Control
* **Category:** IDOR (Insecure Direct Object Reference)
* **Impact:** Unauthorized access to other user accounts

---

## ⚙️ Steps to Reproduce

### 1️⃣ Login as a normal user

```
Username: wiener  
Password: peter
```

---

### 2️⃣ Navigate to account page

You will observe a request like:

```
/my-account?id=<your_GUID>
```

---

### 3️⃣ Analyze the behavior

* The `id` parameter is used to fetch user data
* Each user has a unique GUID
* No authorization check is performed on this parameter

---

### 4️⃣ Identify another user's GUID

Find the GUID of the target user (**carlos**) by analyzing:

* Application responses
* API endpoints
* Any exposed data sources

---

### 5️⃣ Exploit the vulnerability

Replace your GUID with Carlos’s GUID:

```
/my-account?id=<carlos_GUID>
```

---

### 6️⃣ Access sensitive data

* Carlos’s account page becomes accessible
* Extract the **API key**

---

### 7️⃣ Submit solution

Submit the API key → ✅ Lab solved

---

## 🚨 Root Cause

The application only verifies whether the user is authenticated but fails to check whether the user is **authorized** to access the requested resource.

---

## 💥 Impact

* Exposure of sensitive user data
* Account takeover risks
* API key leakage
* Privacy violations

---

## 🛡️ Mitigation

### ✅ Enforce Authorization Checks

Ensure that users can only access their own resources:

```php
if ($request->user()->id !== $requested_id) {
    abort(403);
}
```

---

### ✅ Do Not Trust Client Input

* Never rely on user-supplied identifiers
* Validate access permissions on the server side

---

### ✅ Use Secure Access Control Models

* Implement role-based or object-level access control
* Validate ownership before returning data

---

## 🔥 Key Takeaways

* GUIDs do **not** guarantee security
* Authentication ≠ Authorization
* Always validate access permissions on the server

---

## 🧪 Tools Used

* Burp Suite
* Browser DevTools

---

## 🏁 Conclusion

This lab highlights a critical access control flaw where **unpredictable identifiers fail to provide security in the absence of proper authorization checks**.

Proper server-side validation is essential to prevent unauthorized data access.

---
