Lab: Unprotected Admin Functionality

Platform: PortSwigger Web Security Academy
Category: Access Control
Level: Apprentice

Lab Description

This lab contains an unprotected administrator panel. The objective is to access the admin panel and delete the user carlos.

---

Vulnerability

Broken Access Control

The application exposes an administrator panel without verifying whether the user has administrative privileges. This allows any user to access sensitive administrative functionality.

---

Steps to Solve

1. Discover Hidden Paths

Check the "robots.txt" file to find hidden or disallowed directories.

https://<LAB-ID>.web-security-academy.net/robots.txt

Response:

User-agent: *
Disallow: /administrator-panel

The robots file reveals a hidden administrative path.

---

2. Access the Admin Panel

Navigate directly to the disclosed directory:

https://<LAB-ID>.web-security-academy.net/administrator-panel

Because the application does not enforce proper access control, the administrator panel becomes accessible.

---

3. Delete the Target User

Inside the administrator panel, locate the delete option for the user carlos.

The request typically looks like:

/administrator-panel/delete?username=carlos

Execute the request to delete the user.

---

Result

After deleting the user carlos, the lab is successfully solved.

---

Impact

If this vulnerability existed in a real application, an attacker could:

- Access administrative functionality
- Delete or modify user accounts
- Gain full control over administrative operations

---

Remediation

To prevent this vulnerability:

1. Implement proper server-side access control checks.
2. Restrict administrative endpoints to authorized admin users only.
3. Do not rely on hidden URLs or robots.txt for security.
4. Enforce role-based access control (RBAC).

Example server-side check:

if($user->role !== 'admin'){
    http_response_code(403);
    exit("Forbidden");
}

---

References

OWASP Top 10 – Broken Access Control
PortSwigger Web Security Academy – Access Control Labs
