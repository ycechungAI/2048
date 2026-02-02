## 2024-05-22 - [Reverse Tabnabbing]
**Vulnerability:** External links in `index.html` used `target="_blank"` without `rel="noopener noreferrer"`.
**Learning:** Even simple static sites can expose users to phishing attacks if external links can manipulate the opener window.
**Prevention:** Enforce `rel="noopener noreferrer"` for all `target="_blank"` links.
