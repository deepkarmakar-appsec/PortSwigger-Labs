# 🛡️ Reflected XSS via onfocus + URL Fragment (#) Trick

## 📌 Lab Title

Reflected XSS triggered via focus event using URL fragment

---

## 🎯 Description

This lab is vulnerable to **Reflected Cross-Site Scripting (XSS)** where user input is reflected into the HTML without proper sanitization.
The exploit leverages a **focus-based event (`onfocus`)** combined with a **URL fragment (`#`)** to automatically trigger JavaScript execution.

---

## 🔍 Vulnerability Details

The application reflects user input from the `search` parameter directly into the HTML response.
By injecting a custom HTML element with an event handler, we can execute arbitrary JavaScript.

However, since the payload relies on the `onfocus` event, we must ensure that the element receives focus automatically.

This is achieved using:

* `tabindex` → makes the element focusable
* `#id` fragment → forces the browser to jump to the element and trigger focus

---

## 💥 Payload (Raw)

```html
<xss id=x onfocus=alert(document.cookie) tabindex=1>
```

---

## 🔐 URL Encoded Payload

```
%3Cxss%20id%3Dx%20onfocus%3Dalert(document.cookie)%20tabindex%3D1%3E
```

---

## 🚀 Exploit URL

```
https://yourlabid/?search=%3Cxss%20id%3Dx%20onfocus%3Dalert(document.cookie)%20tabindex%3D1%3E#x
```

---

## ⚙️ Exploit Steps

1. Identify a reflected input parameter (`search`)
2. Inject a custom HTML element with an event handler:

   ```html
   <xss id=x onfocus=alert(document.cookie) tabindex=1>
   ```
3. URL encode the payload
4. Append `#x` to the URL to target the injected element
5. Open the crafted URL

---

## 🧠 Execution Flow

1. The payload is injected via the `search` parameter
2. The server reflects it into the HTML response
3. The browser parses and creates the element with `id="x"`
4. The URL fragment `#x` forces the browser to jump to that element
5. Because of `tabindex`, the element becomes focusable
6. The element receives focus automatically
7. The `onfocus` event triggers
8. JavaScript executes → `alert(document.cookie)`

---

## ⚠️ Impact

* Execution of arbitrary JavaScript
* Session hijacking via cookie theft
* Phishing and malicious redirection
* Account takeover (depending on context)

---

## 🛠️ Mitigation

* Properly sanitize and encode user input
* Use context-aware output encoding
* Implement Content Security Policy (CSP)
* Avoid rendering raw user input into HTML

---

## ✅ Conclusion

This vulnerability demonstrates how combining **HTML injection + browser behavior (URL fragments)** can lead to reliable XSS execution, even without user interaction.

---

## 👨‍💻 Author

Deep Karmakar
Independent Security Researcher
