# 📝 Lab Write-up: User role can be modified in user profile

## 🎯 Objective

The goal of this lab is to gain access to the admin panel located at `/admin`, which is restricted to users with a `roleid` of 2, and use it to delete the user **carlos**.

---

## 🔐 Step 1: Login

First, I logged into the application using the provided credentials:

* Username: wiener
* Password: peter

After logging in successfully, I navigated to the account/profile section to understand how user data is handled.

---

## 🔍 Step 2: Analyzing Profile Update Functionality

Inside the “My Account” section, I found an option to update user details such as email. When I changed the email, I observed that a request was being sent to the server containing user-controlled data.

This looked like a potential entry point for testing access control.

---

## 🧪 Step 3: Intercepting the Request

Using Burp Suite, I intercepted the profile update request. The original request looked something like this:

```
POST /my-account/change-email HTTP/1.1
Content-Type: application/json

{
  "email": "test@gmail.com"
}
```

At this point, I suspected that the application might be vulnerable to parameter manipulation.

---

## 💥 Step 4: Exploiting the Vulnerability

I modified the intercepted request by adding a new parameter:

```
{
  "email": "test@gmail.com",
  "roleid": 2
}
```

Then I forwarded the request to the server.

The server accepted the modified request without any validation and updated my role.

---

## ⚡ Step 5: Confirming Privilege Escalation

In the server response, I noticed that my `roleid` had been changed to 2, which indicates admin-level access.

This confirmed that the application was vulnerable to privilege escalation due to improper access control.

---

## 🧭 Step 6: Accessing the Admin Panel

After becoming an admin, I navigated to:

```
/admin
```

This time, access was granted successfully.

---

## 🗑️ Step 7: Deleting the Target User

Inside the admin panel, I found a list of users. I located the user **carlos** and deleted the account.

This action completed the lab successfully.

---

## 🧠 Root Cause

The vulnerability exists because the server blindly trusts user-supplied input. It allows sensitive fields like `roleid` to be modified directly from the client side without proper authorization checks.

---

## ⚠️ Vulnerability Type

* Broken Access Control
* Mass Assignment

---

## 🛡️ Remediation

To fix this issue, the application should:

* Avoid accepting sensitive parameters like `roleid` from client requests
* Implement proper server-side authorization checks
* Use allowlisting to restrict which fields can be updated

---

## 🔥 Impact

An attacker can escalate their privileges to admin level and gain full control over the application, including performing critical actions such as deleting users.

---

## ✅ Conclusion

By modifying the `roleid` parameter in the profile update request, I was able to escalate privileges and gain admin access. This demonstrates a critical access control flaw that could lead to complete system compromise.
