# On-Chain Address / Contract / DApp Review

## Trigger

- User asks to interact with a blockchain address
- A transaction, swap, or transfer is being prepared
- Agent encounters a smart contract address to evaluate
- A DApp URL is presented for interaction
- Any operation involving movement of funds

## Review Flow

### Step 1: Address Risk Assessment

Before any on-chain interaction, query available risk intelligence:

| Check | Source | What to Look For |
|-------|--------|-----------------|
| **AML risk score** | MistTrack API or similar | Score and risk category |
| **Address labels** | AML databases | Phishing, scam, mixer, sanctioned, exploit |
| **Transaction history** | Block explorer | Age, activity pattern, counterparties |
| **Funding source** | Trace tools | Where did the funds come from? |
| **Associated addresses** | Cluster analysis | Known bad actors in the cluster? |

**Optional:** If MistTrack skills is available, use it to query AML risk data. See external documentation for integration details.

**Risk score thresholds:**

| Score | Risk Level | Action |
|-------|-----------|--------|
| ≤ 30 | 🟢 Low | Proceed with standard caution |
| 31–70 | 🟡 Medium | Show risk summary, recommend caution |
| 71–90 | 🔴 High | Pause, display full risk report, require human confirmation |
| ≥ 91 | ⛔ Severe | **Hard block. Refuse to proceed.** |

**Any address tagged as Phishing, Scam, Sanctioned, or Exploit = automatic 🔴 minimum, regardless of score.**

### Step 2: Smart Contract Review (if applicable)

| Check | What to Look For |
|-------|-----------------|
| **Source verification** | Is the contract source code verified on the block explorer? |
| **Proxy pattern** | Is it an upgradeable proxy? Who controls the upgrade? |
| **Owner/admin functions** | `onlyOwner`, `onlyAdmin` — what can they do? |
| **Approval patterns** | `approve(address, uint256.max)` — unlimited approval requests |
| **Transfer patterns** | `transferFrom`, `safeTransferFrom` — who can move funds? |
| **Self-destruct** | `selfdestruct` or `delegatecall` to untrusted addresses |
| **Hidden mint** | Can the owner mint unlimited tokens? |
| **Blacklist/pause** | Can the owner freeze accounts or pause transfers? |
| **Fee manipulation** | Can trading fees be changed to 100%? |
| **Reentrancy** | External calls before state updates? |

**Unverified source code = cannot audit = 🔴 HIGH minimum.**

### Step 3: DApp Frontend Review (if applicable)

| Check | What to Look For |
|-------|-----------------|
| **Domain** | Official domain? SSL valid? Recently registered? |
| **Wallet permissions** | What permissions does it request? Minimum necessary? |
| **Signature type** | `eth_sign` (dangerous — signs arbitrary data) vs `personal_sign` vs EIP-712 (structured, safer) |
| **Approval scope** | Requesting unlimited token approvals? |
| **Transaction preview** | Does the DApp show what you're signing before confirmation? |
| **Known frontend hijacks** | Check for recent security incidents on the domain |

**Dangerous signature patterns:**
- `eth_sign` — Can sign any data including transactions. **Avoid.**
- `approve(spender, type(uint256).max)` — Unlimited approval. **Always flag.**
- `permit` (EIP-2612) — Gasless approval via signature. Verify the spender.
- `setApprovalForAll` — Approves all NFTs in a collection. **Always flag.**

### Step 4: Rating and Report

**MUST** use [templates/report-onchain.md](../templates/report-onchain.md) template for output.

**Report Requirements:**
- Free-form output is strictly prohibited
- Must include complete assessment framework (AML, transaction, contract analysis)
- Risk rating and verdict must be clear and explicit
- Severe level (≥91) must explicitly reject interaction

**Example:**
```
╔══════════════════════════════════════════════════╗
  ON-CHAIN SECURITY ASSESSMENT
╠══════════════════════════════════════════════════╣
  Address:       0x...
  Chain:         Ethereum
  Type:          EOA
  Label:         [MistTrack returned labels]
╠══════════════════════════════════════════════════╣
  AML RISK ASSESSMENT
  Score:         [0-100]
  Risk Level:    [Low/Moderate/High/Severe]
  Tags:          [Label list]
  Source:        MistTrack
╠══════════════════════════════════════════════════╣
  ...
╚══════════════════════════════════════════════════╝
```

## Common On-Chain Attack Patterns

| Attack | How It Works | Detection |
|--------|-------------|-----------|
| **Address poisoning** | Attacker sends tiny amounts from a similar-looking address, hoping victim copies wrong address | Verify full address, not just first/last characters |
| **Approval phishing** | Trick user into signing unlimited approve() | Check approval amount and spender address |
| **Permit phishing** | Off-chain signature that grants token approval | Decode EIP-2612 permit data before signing |
| **Fake token** | Token with same name/symbol as a real token | Verify contract address against official sources |
| **Honeypot token** | Can buy but cannot sell | Check if sell function has restrictions |
| **Rug pull** | Owner drains liquidity or mints unlimited tokens | Check owner permissions, liquidity lock, mint function |
| **Flash loan attack** | Manipulate price oracle in single transaction | Check oracle design, TWAP vs spot |

## Quick Reference: MistTrack Risk Score Thresholds

| Score | Risk Level | Action | Verdict |
|-------|-----------|--------|---------|
| ≤ 30 | 🟢 Low | Proceed normally, standard caution | ✅ ALLOW |
| 31–70 | 🟡 Moderate | Show risk summary, recommend caution | ⚠️ WARN |
| 71–90 | 🔴 High | Pause, show full report, require confirmation | ⚠️ WARN + CONFIRM |
| ≥ 91 | ⛔ Severe | **Hard block, refuse to proceed** | ❌ BLOCK |

**Auto-escalation Rules:**
- Any Phishing / Scam / Sanctioned / Exploit tag = minimum 🔴 HIGH
- Score = 100 = ⛔ REJECT

## Pre-Transaction Checklist

Before any transaction is submitted:

- [ ] Recipient address verified (full address, not abbreviated)
- [ ] AML risk check completed
- [ ] Amount confirmed by human
- [ ] Gas estimate reasonable (abnormally high gas = potential issue)
- [ ] Contract interaction understood (what function, what parameters)
- [ ] Approval scope minimized (exact amount, not unlimited)
- [ ] Human has confirmed the transaction details
