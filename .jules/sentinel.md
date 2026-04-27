## 2024-05-22 - [Reverse Tabnabbing]
**Vulnerability:** External links in `index.html` used `target="_blank"` without `rel="noopener noreferrer"`.
**Learning:** Even simple static sites can expose users to phishing attacks if external links can manipulate the opener window.
**Prevention:** Enforce `rel="noopener noreferrer"` for all `target="_blank"` links.

## 2026-03-02 - [Content Security Policy]
**Vulnerability:** The application was missing a Content Security Policy (CSP), which could allow attackers to execute malicious scripts or load unauthorized resources if other vulnerabilities like XSS were present.
**Learning:** Even simple static web applications that do not use an external backend can benefit from defense in depth by applying a strict CSP. This app does not use inline scripts or external resources, so a simple `default-src 'self'` policy is sufficient and effective.
**Prevention:** Include a CSP meta tag in the HTML `<head>` for all static web applications.

## 2024-05-23 - [Insecure Deserialization via localStorage]
**Vulnerability:** The application blindly called `JSON.parse` on the `gameState` object stored in `localStorage`, which can be tampered with by an attacker or inadvertently corrupted.
**Learning:** Any data retrieved from `localStorage` should be considered untrusted input. If corrupted data is blindly parsed, it will throw a Javascript exception, crashing the application (Client-side DoS) upon loading.
**Prevention:** Wrap all deserialization logic (`JSON.parse`) in a `try...catch` block. Provide sensible fallback values (e.g., `null`, `0`) if the data format is unexpected or corrupted. Also, ensure parsed primitive values like integers are strictly cast using defensive methods such as `parseInt`.
## 2026-03-23 - [Client-Side DoS via Unhandled JSON.parse]
**Vulnerability:** The application read state from `localStorage` without validating if the contents were well-formed JSON, causing an unhandled `JSON.parse` exception if the local storage data was corrupted or tampered with.
**Learning:** External data sources, even local ones like `localStorage`, should never be blindly trusted. If `localStorage` holds corrupted data (or is tampered with by an extension), the app crashes on load.
**Prevention:** Always wrap `JSON.parse` operations on external data within a `try...catch` block, allowing the application to fail securely (e.g., reset the state or return null) instead of crashing entirely.
## 2024-05-24 - [Insecure Deserialization / Client-side DoS]
**Vulnerability:** The application read game state from `localStorage` and called `JSON.parse()` without error handling. If `localStorage` contained malformed JSON (due to corruption or malicious manipulation), the application would crash and become unplayable.
**Learning:** Client-side storage like `localStorage` should be treated as untrusted input. Malformed data in storage can lead to a client-side Denial of Service if not handled gracefully.
**Prevention:** Always wrap JSON parsing of untrusted data in a `try-catch` block and fail securely (e.g., fallback to default/null state) rather than crashing the application.
## 2024-05-24 - Unhandled JSON.parse in localStorage
**Vulnerability:** A persistent, client-side Denial of Service (DoS) caused by an unhandled `JSON.parse` acting on `localStorage` data, which is completely user-controllable and can easily be modified or corrupted. Additionally, a prototype pollution vector via `hasOwnProperty` existed in `fakeStorage`.
**Learning:** `localStorage` is essentially untrusted input since it exists in the user's browser, and can be tampered with or corrupted due to storage limits. Directly parsing it without `try/catch` causes script execution to halt, making the webapp unusable until local storage is manually cleared.
**Prevention:** Always wrap `JSON.parse` with `try/catch` when dealing with `localStorage`, and fail securely (e.g., reset the data rather than throwing uncaught exceptions). Also use `Object.prototype.hasOwnProperty.call` when checking properties on objects that could potentially have poisoned prototypes.

## 2024-05-24 - [Incomplete Deserialization Validation / Client-side DoS]
**Vulnerability:** The application was already protecting against invalid JSON in `localStorage` via a `try...catch` block. However, it blindly assumed that any valid JSON would contain the exact object structure expected by `GameManager` (e.g., `grid.size`, `grid.cells`). An attacker could manipulate `localStorage` to contain valid JSON with missing properties, causing the app to crash with an unhandled TypeError.
**Learning:** `try...catch` is necessary but not sufficient when deserializing untrusted data. The structure and expected properties of the resulting object must also be verified. Failing to do so can result in unhandled property access errors further down the execution path.
**Prevention:** After deserializing external data, structurally validate the expected object properties before returning it. If the object is malformed, reject it and return a safe default (like `null`).
