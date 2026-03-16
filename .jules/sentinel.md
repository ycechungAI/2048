## 2024-05-22 - [Reverse Tabnabbing]
**Vulnerability:** External links in `index.html` used `target="_blank"` without `rel="noopener noreferrer"`.
**Learning:** Even simple static sites can expose users to phishing attacks if external links can manipulate the opener window.
**Prevention:** Enforce `rel="noopener noreferrer"` for all `target="_blank"` links.

## 2026-03-02 - [Content Security Policy]
**Vulnerability:** The application was missing a Content Security Policy (CSP), which could allow attackers to execute malicious scripts or load unauthorized resources if other vulnerabilities like XSS were present.
**Learning:** Even simple static web applications that do not use an external backend can benefit from defense in depth by applying a strict CSP. This app does not use inline scripts or external resources, so a simple `default-src 'self'` policy is sufficient and effective.
**Prevention:** Include a CSP meta tag in the HTML `<head>` for all static web applications.

## 2024-05-24 - [Insecure Deserialization / Client-side DoS]
**Vulnerability:** The application read game state from `localStorage` and called `JSON.parse()` without error handling. If `localStorage` contained malformed JSON (due to corruption or malicious manipulation), the application would crash and become unplayable.
**Learning:** Client-side storage like `localStorage` should be treated as untrusted input. Malformed data in storage can lead to a client-side Denial of Service if not handled gracefully.
**Prevention:** Always wrap JSON parsing of untrusted data in a `try-catch` block and fail securely (e.g., fallback to default/null state) rather than crashing the application.
