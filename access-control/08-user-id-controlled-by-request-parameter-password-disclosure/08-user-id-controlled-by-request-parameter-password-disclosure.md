# 🧪 Lab Writeup: User ID controlled by request parameter with password disclosure

## 📄 Description

This lab has a user account page where the current user’s password is already filled (masked) inside an input field.

The goal of this lab is simple:

* Get the **administrator’s password**
* Login as administrator
* Delete the user **carlos**

We are given credentials:
wiener : peter

---

## 🚨 Vulnerability Type

Access Control → IDOR (Insecure Direct Object Reference)

---

## 🧠 What’s the Issue?

The application uses a parameter like this:

/my-account?id=wiener

This `id` decides which user’s account data is shown.
But the problem is — there is **no proper authorization check**.

So even though we are logged in as wiener, we can just change the id to another user like:

/my-account?id=administrator

And the application will show that user’s data 😐

Even worse, it exposes the **password in the HTML response** (inside the input field).

---

## ⚔️ Steps to Exploit

### 1. Login

Login using:
wiener : peter

---

### 2. Intercept Request

Open Burp Suite and intercept the request:

GET /my-account?id=wiener

---

### 3. Modify the ID

Now just change the id:

GET /my-account?id=administrator

---

### 4. Check Response

Forward the request and look at the response.

You’ll find something like:

<input type="password" name="password" value="xxxxx" />

That value is the **administrator’s password** 🔥

---

### 5. Login as Administrator

Use the extracted password and login as administrator.

---

### 6. Delete Carlos

After logging in:

* Go to admin panel
* Delete user **carlos**

---

## ✅ Lab Solved

---

## 💥 Impact

This is a serious vulnerability because:

* Any user can access other users’ data
* Passwords are exposed in plaintext
* Leads to full account takeover
* Easy privilege escalation to admin

---

## 🛡️ How to Fix

* Always check authorization on server side
* Never trust user-controlled parameters like `id`
* Never expose passwords in responses
* Store passwords securely using hashing

---

## 🧠 Key Learning

Small mistakes in access control can lead to complete system compromise.
Always test parameters like `id`, `user`, `account` for IDOR.

---

## 🚀 Tags

#IDOR #AccessControl #CyberSecurity #AppSec #BugBounty #PortSwigger
