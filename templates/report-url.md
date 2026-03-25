# URL / Document Security Assessment Report Template

Use this template when reporting the results of a url-document.md review.

---

```
════════════════════════════════════
  URL / DOCUMENT SECURITY ASSESSMENT
────────────────────────────────────
  URL:            [original URL]
  Final URL:      [after redirects, if different]
  Domain:         [domain name]
  Content-Type:   [text/markdown, text/html, etc.]
  Fetched At:     [timestamp]
────────────────────────────────────
  PROMPT INJECTION SCAN
  Code blocks found:     [n]
  Attack vectors found:  [n] / 17 categories
  
  Detected vectors:
  [None]
  — or —
  • [#n] [vector name]: [description]
  • [#n] [vector name]: [description]
────────────────────────────────────
  SOCIAL ENGINEERING PATTERNS
  [None detected]
  — or —
  • [pattern type]: [specific instance found]
────────────────────────────────────
  CONTENT SUMMARY
  [Brief description of what the document claims to be about]
  [What the document actually does if code blocks were executed]
────────────────────────────────────
  RISK:     [🟢 LOW / 🟡 MEDIUM / 🔴 HIGH / ⛔ REJECT]
  VERDICT:  [✅ SAFE / ⚠️ CAUTION / ❌ REJECT]
────────────────────────────────────
  NOTES
  [Key observations]
════════════════════════════════════
```

## Field Guidelines

- **Attack vectors**: Reference the 17 categories from reviews/url-document.md
- **Social engineering patterns**: Reference categories from patterns/social-engineering.md
- **Content Summary**: Two parts — (1) what it claims to be, (2) what it actually does
- **Final URL**: Note if the URL redirected, and whether the redirect was suspicious
