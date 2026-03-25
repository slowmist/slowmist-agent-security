# Skill / MCP Security Assessment Report Template

Use this template when reporting the results of a skill-mcp.md review.

---

```
════════════════════════════════════
  SKILL / MCP SECURITY ASSESSMENT
────────────────────────────────────
  Name:         [skill-name]
  Version:      [x.y.z]
  Source:       [clawhub / github / npm / url]
  Author:       [name or organization]
  Trust Tier:   [1-5] — [description]
  Published:    [date]
  Last Updated: [date]
────────────────────────────────────
  FILES SCANNED
  Total: [n]  |  Executable: [n]  |  Docs: [n]
  High-risk files: [list or "None"]
────────────────────────────────────
  RED FLAGS
  [None]
  — or —
  • [flag-1]: [description] (Severity: 🔴/🟡)
  • [flag-2]: [description] (Severity: 🔴/🟡)
────────────────────────────────────
  PERMISSIONS REQUIRED
  Read:     [files/directories or "None"]
  Write:    [files/directories or "None"]
  Network:  [domains/endpoints or "None"]
  System:   [commands or "None"]
  Env Vars: [variable names or "None"]
────────────────────────────────────
  ARCHITECTURE
  Credential handling:  [description]
  Human-in-the-loop:    [Yes/No — detail]
  Auto-update:          [Yes/No — detail]
  Data boundary:        [Local only / Sends to X]
  Degradation:          [Graceful / Undefined]
────────────────────────────────────
  RISK:     [🟢 LOW / 🟡 MEDIUM / 🔴 HIGH / ⛔ REJECT]
  VERDICT:  [✅ SAFE / ⚠️ CAUTION / ❌ REJECT]
────────────────────────────────────
  NOTES
  [Key observations, context, recommendations]
════════════════════════════════════
```

## Field Guidelines

- **Trust Tier**: Reference SKILL.md trust hierarchy (1=official org, 5=unknown)
- **High-risk files**: List any .sh, .js, .py, .mjs, .elf, .so, .wasm, .tar.gz files
- **Red Flags**: Reference specific patterns from patterns/red-flags.md
- **Permissions**: Be specific — not "reads files" but "reads ~/.openclaw/openclaw.json"
- **Architecture**: Only fill these fields if the skill interacts with external services
- **Notes**: Include false positive explanations if applicable
