# Repository Security Assessment Report Template

Use this template when reporting the results of a repository.md review.

---

```
════════════════════════════════════
  REPOSITORY SECURITY ASSESSMENT
────────────────────────────────────
  Repository:    [owner/repo]
  URL:           [https://github.com/...]
  Stars / Forks: [n] / [n]
  Created:       [date]
  Last Commit:   [date]
  Contributors:  [n]
  License:       [license]
  Trust Tier:    [1-5] — [description]
────────────────────────────────────
  SCOPE
  Language:      [primary language(s)]
  Purpose:       [what the project does]
  Files Audited: [n] / [total n]
────────────────────────────────────
  SECURITY FINDINGS
  [None]
  — or —
  • [ID] [SEVERITY] [title]
    [description]
    [affected file:line]
    [impact]
────────────────────────────────────
  ARCHITECTURE ASSESSMENT
  Authentication:  [description]
  Authorization:   [description]
  Data flow:       [description]
  Secret mgmt:     [description]
  Dependencies:    [n total, n with known CVEs]
  Update mechanism: [description]
────────────────────────────────────
  RISK:     [🟢 LOW / 🟡 MEDIUM / 🔴 HIGH / ⛔ REJECT]
  VERDICT:  [summary recommendation]
────────────────────────────────────
  NOTES
  [Key observations, context, comparison with alternatives]
════════════════════════════════════
```

## Field Guidelines

- **Security Findings**: Use a unique ID per finding (e.g., REPO-01, REPO-02)
- **Severity**: CRITICAL / HIGH / MEDIUM / LOW / INFO
- **Files Audited**: Focus on entry points, auth, API handlers, install scripts
- **Dependencies**: Note if versions are pinned, use ranges, or use `latest`
