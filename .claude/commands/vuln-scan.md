You are a security vulnerability scanner for this project: a single-file HTML application (`application-form.html`) with all HTML, CSS, and JavaScript inline. There is no build system or package manager.

Read `application-form.html` in full, then perform the checks below. Report every finding — do not skip low-severity items.

---

## Severity Scale

Assign one of these levels to each finding, based solely on exploitability:

| Severity | Criteria |
|----------|----------|
| **CRITICAL** | Directly exploitable with no user interaction; leads to full data/session compromise (e.g. stored XSS, arbitrary code execution) |
| **HIGH** | Exploitable with minimal user interaction or easily met conditions; significant data exposure or privilege escalation |
| **MEDIUM** | Requires specific conditions or partial user interaction; limited blast radius |
| **LOW** | Exploitable only in edge cases or with chained prerequisites; minimal direct impact |
| **INFO** | No direct exploitability; deviates from best practice and may elevate risk if the codebase grows |

---

## Scan Checklist

### First-Party Code

**XSS & Injection**
- [ ] Any use of `innerHTML`, `outerHTML`, `insertAdjacentHTML`, `document.write`, or `eval` with unsanitized user input
- [ ] `textContent` / `setAttribute` used safely vs. raw HTML injection paths
- [ ] URL parameters or hash fragments reflected into the DOM without encoding
- [ ] Event handler attributes (`onclick=`, `onload=`, etc.) constructed from user data

**Input Validation**
- [ ] All form fields validated before use (check each `validateField` call and its check function)
- [ ] Numeric fields: are type coercions safe? Can NaN, Infinity, or out-of-range values pass?
- [ ] String fields: are there regex bypasses (e.g. Unicode lookalikes, newlines, null bytes)?
- [ ] Is validation client-side only? Note if there is no server-side counterpart (informational)

**Data Handling**
- [ ] Sensitive values (names, emails) stored in `localStorage` / `sessionStorage` / cookies without need
- [ ] Reference numbers or IDs predictable or guessable in a meaningful way
- [ ] Any data sent to an external endpoint without the user's awareness

**DOM & Logic**
- [ ] Clickjacking: is an `X-Frame-Options` or CSP `frame-ancestors` meta tag present?
- [ ] `window.print()` or other browser APIs called in exploitable ways
- [ ] Any `postMessage` listeners without origin checks
- [ ] `setTimeout` / `setInterval` with string arguments (implicit eval)

**Print / Media Contexts**
- [ ] `@media print` rules — do they accidentally expose hidden data or remove security-relevant UI?

---

### Third-Party Code

**External Resources**
- [ ] List every `<script src>`, `<link href>`, `<img src>`, `<iframe src>`, and `fetch`/`XMLHttpRequest` call pointing to an external origin
- [ ] For each external script/stylesheet: is a `integrity="sha256-..."` (SRI) attribute present?
- [ ] Are external URLs using HTTPS?
- [ ] Are any CDN-hosted libraries loaded without a pinned version (e.g. `@latest`)?

**Known Vulnerable Libraries**
- [ ] Identify any library name + version loaded from a CDN
- [ ] Flag any library with a known CVE matching that version range

---

## Output Format

Group findings by severity (CRITICAL first). For each finding:

```
[SEVERITY] Short Title
  Location : file, line number(s) or selector
  What     : what the code does
  Risk     : how it could be exploited and by whom
  Fix      : the minimal change that resolves it
```

After all findings, print a **Summary Table**:

```
CRITICAL : N
HIGH     : N
MEDIUM   : N
LOW      : N
INFO     : N
─────────────
TOTAL    : N
```

If a category has zero findings, still include it in the table with `0`.

---

If no arguments are provided, scan the entire file. If the user passes a specific element, function, or section as `$ARGUMENTS`, focus the scan on that scope but still run all applicable checks.

ARGUMENTS: $ARGUMENTS
