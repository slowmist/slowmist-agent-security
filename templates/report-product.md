# Product / Service Security Assessment Report Template

Use this template when reporting the results of a product-service.md review.

---

```
════════════════════════════════════
  PRODUCT / SERVICE SECURITY ASSESSMENT
────────────────────────────────────
  Name:          [product/service name]
  Provider:      [company/org]
  URL:           [official URL]
  Type:          [API / SDK / SaaS / Platform / Skill]
  Trust Tier:    [1-5] — [description]
────────────────────────────────────
  ARCHITECTURE ANALYSIS
  Key Management:
    Model:       [User-held / Local agent / Custodial / Remote]
    Storage:     [Hardware wallet / Encrypted / Plaintext .env / Memory]
    Rotation:    [Supported / Not supported]
  
  Human-in-the-Loop:
    Write ops:   [Requires CONFIRM / Automatic]
    Large ops:   [Additional warning / No protection]
    Limits:      [Local / Remote / None]
  
  Data Flow:
    Data stays:  [Local only / Sent to service / Sent to third party]
    Encryption:  [TLS + at-rest / TLS only / None]
    Retention:   [Policy description]
  
  Update Mechanism:
    Type:        [Manual / Notify + confirm / Auto-download / Silent]
    Signed:      [Yes / No]
────────────────────────────────────
  PERMISSIONS REQUIRED
  [Detailed list of required permissions and their justification]
  
  Worst-case if compromised:
  [What happens if credentials for this service are leaked?]
────────────────────────────────────
  RED FLAGS
  [None]
  — or —
  • [flag]: [description]
────────────────────────────────────
  RISK:     [🟢 LOW / 🟡 MEDIUM / 🔴 HIGH / ⛔ REJECT]
  VERDICT:  [✅ SAFE / ⚠️ USE WITH RESTRICTIONS / ❌ REJECT]
────────────────────────────────────
  RECOMMENDED RESTRICTIONS (if ⚠️ CAUTION)
  • [restriction-1]
  • [restriction-2]
────────────────────────────────────
  NOTES
  [Key observations, comparison, alternatives]
════════════════════════════════════
```

## Field Guidelines

- **Key Management Model**: See product-service.md for the 4-level classification
- **Human-in-the-Loop**: Detail what requires confirmation and what's automatic
- **Worst-case**: Be specific — "total fund loss up to account balance" not just "bad"
- **Recommended Restrictions**: Practical steps to reduce risk if the user still wants to use it (e.g., sub-account, IP binding, read-only API key)
