# GitHub Repository Review

## Trigger

- User sends a GitHub repository link for evaluation
- Agent encounters a repo link in context and needs to assess it
- Repository is referenced as a dependency or data source

## Review Flow

### Step 1: Repository Metadata

Collect and assess:

| Metric | What to Check |
|--------|--------------|
| **Stars / Forks** | Popularity signal (can be faked — see note below) |
| **Created date** | Brand new repos deserve more scrutiny |
| **Last commit** | Abandoned repos may have unpatched vulnerabilities |
| **Contributors** | Single-person vs team; known identities vs anonymous |
| **Issues / PRs** | Active community or ghost town? Security issues reported? |
| **License** | Present and standard? Unusual license terms? |
| **Organization** | Official org account or personal? Verified? |
| **Forks of** | Is this a fork? Compare diff with upstream |

**Star count warning:** Stars can be purchased. A repo with 10,000 stars but 2 contributors, no issues, and created last week is suspicious. Cross-reference with actual community activity.

### Step 2: Code Security Audit

**Priority files to review:**

| File | Why |
|------|-----|
| Entry points (main.js, app.py, index.ts) | Core logic, most likely to contain vulnerabilities |
| Configuration (config.*, .env.example) | Credential handling, default settings |
| Authentication/authorization modules | Access control logic |
| API route handlers | Input validation, injection points |
| Middleware | Request processing, auth checks |
| CI/CD (.github/workflows, Dockerfile) | Build-time attacks, secret exposure |
| Install scripts (postinstall, setup.py) | Supply chain entry points |
| Dependencies (package.json, requirements.txt, Cargo.toml) | Known vulnerable dependencies |

**Apply [patterns/red-flags.md](../patterns/red-flags.md) to all reviewed files.**

### Step 3: Architecture Analysis

| Aspect | Questions |
|--------|-----------|
| **Authentication** | How does it authenticate? Default credentials? Auth bypass possible? |
| **Authorization** | Role-based access? Privilege escalation paths? |
| **Data flow** | Where does user data go? Encrypted in transit/at rest? |
| **Input validation** | SQL injection, XSS, command injection, path traversal? |
| **Dependency chain** | How many dependencies? Any known CVEs? Pinned versions? |
| **Secret management** | How are secrets stored? Hardcoded? Environment? Vault? |
| **Error handling** | Does it leak information in error messages? |
| **Update mechanism** | Auto-update? Signature verification? |

### Step 4: Rating and Report

Output using [templates/report-repo.md](../templates/report-repo.md).

## Common Repository Red Flags

| Signal | Concern |
|--------|---------|
| Empty `api_key` config = skip auth | Default-open access pattern |
| WebSocket without origin validation | Cross-site WebSocket hijacking |
| `exec_policy.mode = "Full"` bypasses security checks | Agent manifest can disable protections |
| Unauthenticated API endpoints for sensitive data | Information disclosure |
| `eval()` on user-supplied input | Remote code execution |
| SQL queries with string concatenation | SQL injection |
| File paths from user input without sanitization | Path traversal |
| CORS with `Access-Control-Allow-Origin: *` | Cross-origin data theft |
| JWT without expiration or with weak secret | Authentication bypass |
| Dockerfile running as root | Container escape risk |

## Fork Analysis

When reviewing a fork:
1. Identify the upstream repository
2. Run `git diff upstream/main..fork/main` mentally or via API
3. Focus audit on **changed files only** — the fork's modifications are the risk surface
4. A fork that only changes configuration, API endpoints, or adds new dependencies is highly suspicious
