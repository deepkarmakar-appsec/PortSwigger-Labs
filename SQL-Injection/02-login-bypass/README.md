# Lab 02 – SQL Injection: Login Bypass

## Lab Information
- Platform: PortSwigger Web Security Academy
- Vulnerability: SQL Injection (Authentication Bypass)
- Difficulty: Apprentice
- Status: Solved ✅

---

## Description
This lab contains a SQL injection vulnerability in the login functionality.
User input is directly used in the SQL query without proper sanitization.
By exploiting this vulnerability, an attacker can bypass authentication
and log in as the administrator user.

---

## Vulnerable Functionality
```http
POST /login
