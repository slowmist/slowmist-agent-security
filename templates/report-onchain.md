# On-Chain Security Assessment Report Template

Use this template when reporting the results of an onchain.md review.

---

```
════════════════════════════════════
  ON-CHAIN SECURITY ASSESSMENT
────────────────────────────────────
  Address:       [0x... or chain-specific format]
  Chain:         [Ethereum / BSC / Solana / etc.]
  Type:          [EOA / Contract / Multisig]
  Label:         [Known labels or "Unknown"]
────────────────────────────────────
  AML RISK ASSESSMENT
  Score:         [0-100]
  Risk Level:    [Low / Medium / High / Severe]
  Tags:          [Phishing / Mixer / Sanctioned / Clean / etc.]
  Source:        [MisTrack / Chainalysis / etc.]
────────────────────────────────────
  TRANSACTION PROFILE
  First Active:  [date]
  Last Active:   [date]
  Total Txns:    [n]
  Notable:       [large transfers, patterns, counterparties]
────────────────────────────────────
  CONTRACT ANALYSIS (if applicable)
  Verified:       [Yes / No]
  Proxy:          [Yes / No — upgrade authority]
  Owner Perms:    [list of privileged functions]
  Approval Risk:  [unlimited approve / permit / setApprovalForAll]
  Known Vulns:    [list or "None identified"]
────────────────────────────────────
  RISK:     [🟢 LOW / 🟡 MEDIUM / 🔴 HIGH / ⛔ REJECT]
  VERDICT:  [✅ SAFE TO INTERACT / ⚠️ CAUTION / ❌ DO NOT INTERACT]
────────────────────────────────────
  NOTES
  [Key observations, related addresses, recommendations]
════════════════════════════════════
```

## Field Guidelines

- **AML Score**: Apply thresholds from reviews/onchain.md (≤30 Low, 31-70 Medium, 71-90 High, ≥91 Severe)
- **Tags**: Any Phishing/Scam/Sanctioned/Exploit tag = automatic 🔴 minimum
- **Contract Analysis**: Only fill if the address is a contract
- **Approval Risk**: Flag any unlimited approvals or setApprovalForAll
- **Related Addresses**: Note if the address is part of a known cluster
