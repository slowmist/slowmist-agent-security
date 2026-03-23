# SlowMist Agent Security Skill 🛡️

A comprehensive security review framework for AI agents operating in adversarial environments.

> **Core principle: Every external input is untrusted until verified.**

## Overview

This skill provides a structured security review framework for OpenClaw agents, covering:

- **Skill/MCP Installation** — Detect malicious patterns before installation
- **GitHub Repository Review** — Audit codebases for security issues
- **URL/Document Analysis** — Scan for prompt injection and social engineering
- **On-Chain Address Review** — AML risk assessment and transaction analysis
- **Product/Service Evaluation** — Architecture and permission analysis
- **Social Share Review** — Validate tools recommended in chats

## Installation

### Option 1: Direct Download
Download the latest release and extract to your OpenClaw workspace:
```bash
cd ~/.openclaw/workspace/skills
git clone https://github.com/slowmist/slowmist-agent-security.git
```

### Option 2: ClawHub (when available)
```bash
clawhub install slowmist-agent-security
```

## Quick Start

Once installed, the agent will automatically reference this framework when encountering:
- Skill/MCP installation requests
- Unknown GitHub repositories
- External URLs or documents
- Blockchain addresses
- Product/service recommendations

## Framework Structure

```
slowmist-agent-security/
├── SKILL.md                    # Main framework documentation
├── README.md                   # This file
├── _meta.json                  # ClawHub metadata
├── reviews/
│   ├── skill-mcp.md           # Skill/MCP review guide
│   ├── repository.md          # GitHub repo review guide
│   ├── url-document.md        # URL/document review guide
│   ├── onchain.md             # On-chain address review guide
│   ├── product-service.md     # Product/service review guide
│   └── message-share.md       # Social share review guide
├── patterns/
│   ├── red-flags.md           # Code-level dangerous patterns (11 categories)
│   ├── social-engineering.md  # Social engineering patterns (8 categories)
│   └── supply-chain.md        # Supply chain attack patterns (7 categories)
└── templates/
    ├── report-skill.md        # Skill assessment report template
    ├── report-repo.md         # Repository assessment report template
    ├── report-url.md          # URL/document assessment report template
    ├── report-onchain.md      # On-chain assessment report template
    └── report-product.md      # Product/service assessment report template
```

## Risk Rating System

| Level | Meaning | Agent Action |
|-------|---------|--------------|
| 🟢 LOW | Information-only, no execution, no data collection, trusted source | Inform user, proceed if requested |
| 🟡 MEDIUM | Limited capability, clear scope, known source, some risk | Full report with risk items, recommend caution |
| 🔴 HIGH | Involves credentials, funds, system modification, unknown source | Detailed report, **must have human approval** |
| ⛔ REJECT | Matches red-flag patterns, confirmed malicious, unacceptable design | Refuse to proceed, explain why |

## Trust Hierarchy

| Tier | Source Type | Scrutiny Level |
|------|-------------|----------------|
| 1 | Official project/exchange org | Moderate |
| 2 | Known security teams/researchers | Moderate |
| 3 | ClawHub high-download + multi-version | Moderate-High |
| 4 | GitHub high-star + actively maintained | High — verify code |
| 5 | Unknown source, new account | Maximum scrutiny |

## Optional Integration

- **MistTrack Skills** — For on-chain AML risk assessment (external tool)

## Usage Examples

### Example 1: Skill Review
When a user asks to install a skill:
1. Reference `reviews/skill-mcp.md`
2. Scan files using `patterns/red-flags.md`
3. Output report using `templates/report-skill.md`

### Example 2: On-Chain Address Review
When a user provides a blockchain address:
1. Validate address format
2. Query AML risk data (via available tools)
3. Output report using `templates/report-onchain.md`

## Contributing

This framework is maintained by SlowMist. Contributions welcome:
- New attack patterns
- Improved detection rules
- Additional review templates

## Credits

- Inspired by [skill-vetter](https://clawhub.ai/spclaudehome/skill-vetter) by spclaudehome
- Attack patterns informed by the [OpenClaw Security Practice Guide](https://github.com/slowmist/openclaw-security-practice-guide)
- Prompt injection patterns based on real-world PoC research

## License

MIT License — Free to use, modify, and distribute.

---

*Security is not a feature — it's a prerequisite.* 🛡️

**SlowMist** · https://slowmist.com
