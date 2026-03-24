## 🧪 Lab: Unprotected Admin Functionality with Unpredictable URL

Platform: PortSwigger Web Security Academy  
Category: Access Control  
Level: Apprentice  

---

## 📌 Lab Description

This lab contains an unprotected administrator panel located at an unpredictable URL.  
The goal is to discover the admin panel and delete the user "carlos".

---

## 🔍 Vulnerability

**Broken Access Control**

The application exposes an admin panel without enforcing proper server-side authorization.  
The admin functionality is only hidden using an unpredictable URL, which can still be discovered from the client-side.

---

## 🔎 Steps to Solve

### 1. Discover the Hidden Admin URL

I started by inspecting the page source (View Page Source) and JavaScript files.

While analyzing the code, I found a hidden path:

/admin-54edq0

This shows that the admin panel is not secured, only obscured.

---

### 2. Access the Admin Panel

I directly navigated to:

https://<LAB-ID>.web-security-academy.net/admin-54edq0

The admin panel loaded successfully without any authentication or authorization checks.

---

### 3. Delete the Target User

Inside the admin panel, I located the delete functionality for users.

The request looked like:

/admin-54edq0/delete?username=carlos

I executed the request, and the user "carlos" was successfully deleted.

---

## ✅ Result

After deleting the user "carlos", the lab was successfully solved.

---

## ⚠️ Impact

If this vulnerability exists in a real-world application:

- Unauthorized users can access admin functionality  
- Users can be deleted or modified  
- Full system compromise is possible  

---

## 🔐 Remediation

To prevent this issue:

1. Implement proper server-side access control checks  
2. Restrict admin endpoints to authorized users only  
3. Do not rely on hidden or unpredictable URLs for security  
4. Enforce role-based access control (RBAC)  

Example:

if ($user->role !== 'admin') {
    abort(403);
}

---

## 📚 References

- OWASP Top 10 – Broken Access Control  
- PortSwigger Web Security Academy – Access Control Labs  
