# SQL Injection – WHERE Clause Allows Retrieval of Hidden Data

## Lab Information
- Platform: PortSwigger Web Security Academy
- Vulnerability: SQL Injection
- Difficulty: Apprentice
- Status: Solved ✅

---

## Description
This lab contains a SQL injection vulnerability in the product category filter.
User input is directly used in a SQL query without proper sanitization.

An attacker can exploit this vulnerability to retrieve hidden or unreleased data.

---

## Vulnerable Endpoint
```http
GET /filter?category=Gifts
