# How Websites Work

**Platform** : TryHackMe  
**Module** : How The Web Works  
**Difficulty** : Easy  
**Status** : ✅ Completed  

---

## Objective

Understand how websites are built and identify the most common client-side vulnerabilities: sensitive data exposure and HTML injection.

---

## Task 1 — How Websites Work

A browser sends a request to a server, which returns the files that make up the page. Two main components:

* **Frontend (client-side)**: what the browser receives and renders (HTML, CSS, JavaScript)
* **Backend (server-side)**: the server that processes data and generates responses

---

## Task 2 — HTML

HTML (HyperText Markup Language) is the skeleton of a web page. It defines the structure through nested tags.

Basic structure:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Page Title</title>
    </head>
    <body>
        <h1>Main Heading</h1>
        <p>A paragraph</p>
    </body>
</html>
```

Key points:
* Tags have attributes: `class`, `id`, `src`, `href`
* `<button onclick="...">` : JavaScript events are attached directly to HTML elements

---

## Task 3 — JavaScript

JavaScript (JS) makes pages dynamic and interactive. It can modify the DOM in real time.

Example: content manipulation via `innerHTML`

```javascript
document.getElementById("demo").innerHTML = "Hack the Planet";
```

Common mistake — placing code inside the `type` attribute instead of the tag body:

```html
<!-- WRONG -->
<script type=document.getElementById("demo").innerHTML = "Hack the Planet"</script>

<!-- CORRECT -->
<script>
    document.getElementById("demo").innerHTML = "Hack the Planet";
</script>
```

---

## Task 4 — Sensitive Data Exposure

### Concept

Sensitive data exposure occurs when a developer leaves confidential information in the source code accessible to the browser: credentials, tokens, admin URLs, API keys.

Anyone can view this code without special tools:

* `Ctrl+U` (Firefox / Chrome) — view page source
* `F12` > Sources tab

### What to look for

```html
<!-- username: admin password: example123 -->
```

```javascript
var apiKey = "sk-supersecret";
var adminUrl = "/admin/panel";
```

### References

| Framework | Control |
|---|---|
| OWASP Top 10 | A02 — Cryptographic Failures |
| CWE | CWE-312 — Cleartext Storage of Sensitive Information |
| ISO 27001 | A.8.10 — Information deletion / A.8.28 — Secure coding |

### TryHackMe Questions

**Q: What is the password hidden in the source code?**  
Answer: found via `Ctrl+U` on the task site, inside an HTML comment or a JavaScript variable.

---

## Task 5 — HTML Injection

### Concept

HTML injection occurs when a web application displays user input directly on the page without sanitising it. The browser then interprets the injected content as real HTML.

### Attack vector

If a form field renders the input as-is on the page:

```html
<!-- Normal input -->
Hello, Pierre!

<!-- Malicious input -->
<a href="http://hacker.com">Click here</a>
```

The browser will render the link instead of displaying it as plain text.

### Payload used

```html
<a href="http://hacker.com">click here</a>
```

### Why it works

The developer passes the input directly to `innerHTML` without filtering:

```javascript
// Vulnerable
document.getElementById("output").innerHTML = userInput;

// Secure
document.getElementById("output").innerText = userInput;
```

The difference: `innerHTML` interprets HTML tags, `innerText` treats them as plain text.

### Possible escalation

If the application does not block JavaScript either:

```html
<script>window.location = "http://hacker.com"</script>
```

This vector leads directly to Cross-Site Scripting (XSS), a major vulnerability covered in dedicated rooms.

### References

| Framework | Control |
|---|---|
| OWASP Top 10 | A03 — Injection |
| CWE | CWE-79 — Cross-site Scripting / CWE-80 — HTML Injection |
| ISO 27001 | A.8.28 — Secure coding |
| NIS2 | Art. 21 — Cybersecurity risk management measures |

---

## Vulnerability Summary

| Vulnerability | Root Cause | Impact | Remediation |
|---|---|---|---|
| Sensitive Data Exposure | Credentials stored in plain text in client-side source code | Account compromise / unauthorized access | Never store secrets client-side |
| HTML Injection | No input sanitisation | Phishing, redirection, defacement | Use `innerText` instead of `innerHTML`, encode output |

---

## GRC Takeaways

* **Input validation** is a fundamental requirement of every secure coding standard (ISO 27001 A.8.28, OWASP ASVS).
* **Application security testing** (code review, SAST, penetration testing) must systematically cover these attack vectors before production deployment.
* In an audit context, frontend source code review is a baseline control for any exposed web application.
