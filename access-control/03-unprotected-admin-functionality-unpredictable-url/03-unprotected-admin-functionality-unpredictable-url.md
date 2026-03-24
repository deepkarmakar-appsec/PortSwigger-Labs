## Lab: User Role Controlled by Request Parameter

### Vulnerability
Broken Access Control

---

### Steps to Solve

1. Login using:
   wiener:peter

2. Intercept the request in Burp Suite and check the cookies  

   Found:
   Admin=false

3. Modify the cookie:
   Admin=true

4. Open:
   /admin  

   Admin panel is now accessible

5. Delete the user:
   /admin/delete?username=carlos  

---

### Result
User "carlos" was deleted and the lab was solved.

---

### Impact
- Normal user can become admin  
- Users can be deleted or modified  
- Full system compromise possible  

---

### Fix
- Implement server-side role checks  
- Do not trust client-side data (cookies)

Example:
if ($user->role !== 'admin') {
    abort(403);
}
