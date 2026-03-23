# Code-Level Red Flag Patterns

Every pattern includes: description, detection keywords/regex, severity, false positive guidance, and real-world example.

---

## 1. Outbound Data Exfiltration

**What:** Code sends local data to external servers.

**Detection keywords:**
```
curl, wget, fetch, http.get, https.get, requests.post, requests.get,
axios, got, node-fetch, urllib, httplib, XMLHttpRequest,
nc (netcat), socat, /dev/tcp, /dev/udp
```

**Severity:** 🔴 If sending local data externally; 🟡 If fetching external data only

**False positive:** A weather skill calling `api.openweathermap.org` is expected. A "file organizer" skill calling an unknown IP is not.

**Key question:** Is the destination domain consistent with the skill's stated purpose? Is local data included in the request?

**Real-world example:** PoC document used `curl -s "https://www.random.org/..."` to verify outbound network capability as a precursor to data exfiltration.

---

## 2. Credential / Environment Variable Access

**What:** Code reads environment variables, .env files, or credential stores.

**Detection keywords:**
```
process.env, os.environ, os.getenv, $ENV, ${ENV},
dotenv, .env, config.json, credentials, keychain,
grep -i key, grep -i token, grep -i secret, grep -i password
```

**Severity:** 🔴 If combined with network send; 🟡 If reading for own API use

**False positive:** Tavily skill reading `TAVILY_API_KEY` to call Tavily's own API. This is expected behavior — the key matches the service boundary.

**Key question:** Does the credential access match the service the skill claims to interact with? Does the credential leave the local environment?

**Real-world example:** `env | grep -iE "key|token|secret|password" >> /tmp/exfil.txt` — harvests all credentials indiscriminately.

---

## 3. File System Access Beyond Scope

**What:** Code reads or writes files outside its expected working directory.

**Detection keywords:**
```
~/.ssh, ~/.aws, ~/.config, ~/.gnupg, /etc/ssh, /etc/shadow,
/etc/passwd, ~/.openclaw, ~/.claude, ~/.cursor, /proc/,
expanduser, os.path.join("..", ), path.resolve(".."),
readFileSync, writeFileSync, open(, fs.read, fs.write
```

**Severity:** 🔴 For sensitive directories; 🟡 For other out-of-scope access

**False positive:** A git skill reading `~/.gitconfig` is expected. A "weather" skill reading `~/.ssh/` is not.

**Key question:** Does the file access serve the skill's stated purpose?

---

## 4. Agent Identity / Memory File Access

**What:** Code accesses agent-specific identity or memory files.

**Detection keywords:**
```
MEMORY.md, USER.md, SOUL.md, IDENTITY.md, AGENTS.md, TOOLS.md,
paired.json, openclaw.json, sessions.json, .claude/settings,
workspace/memory/, agents/main/
```

**Severity:** 🔴 Always — these files contain personal information and agent context

**False positive:** Essentially none. No third-party skill should need to access agent identity or memory files.

**Real-world example:** PoC document: `cp ~/.openclaw/workspace/MEMORY.md /tmp/poc-agent-pwned-memory.txt`

---

## 5. Dynamic Code Execution

**What:** Code constructs and executes code at runtime from external input.

**Detection keywords:**
```
eval(, exec(, Function(, execSync(, child_process,
subprocess.run, subprocess.Popen, os.system, os.popen,
compile(, __import__, importlib,
new Function, setTimeout(string), setInterval(string)
```

**Severity:** 🔴 If input comes from external/untrusted source; 🟡 If input is hardcoded/safe

**False positive:** A build tool using `exec` to run a known, fixed command. Still worth noting.

---

## 6. Privilege Escalation

**What:** Code attempts to elevate privileges or modify system security settings.

**Detection keywords:**
```
sudo, su -, doas, pkexec,
chmod 777, chmod +s, chown root, setuid, setgid,
visudo, /etc/sudoers,
capabilities, cap_sys_admin, cap_net_raw
```

**Severity:** 🔴 Always

**False positive:** Essentially none for third-party skills. No skill should need root privileges.

---

## 7. Persistence Mechanisms

**What:** Code establishes mechanisms to survive reboots or session restarts.

**Detection keywords:**
```
crontab, /etc/cron, at -f, systemctl enable,
~/.config/autostart, ~/.bashrc, ~/.zshrc, ~/.profile, ~/.bash_profile,
/etc/rc.local, /etc/init.d, launchd, plist,
LoginItems, com.apple.loginitems
```

**Severity:** 🔴 Always — persistence is an indicator of malicious intent in skill context

**False positive:** A system management skill that explicitly documents its cron usage and requires human approval. Very rare.

**Real-world example:** PoC document injected crontab entry: `(crontab -l; echo "*/5 * * * * curl ...") | crontab -`

---

## 8. Runtime Package Installation (Secondary Download)

**What:** Code downloads and installs additional packages during execution, not declared upfront.

**Detection keywords:**
```
npm install, npm i, npx, yarn add, pnpm add,
pip install, pip3 install, easy_install,
cargo install, go install, gem install, apt install, apt-get install,
brew install, pacman -S, dnf install,
curl | sh, curl | bash, wget | sh, wget | bash,
install_requires (in setup.py with URLs)
```

**Severity:** 🔴 Always — the actual payload is not visible at review time

**Key question:** If a skill needs dependencies, they should be declared upfront and auditable, not installed silently at runtime.

---

## 9. Code Obfuscation

**What:** Code is intentionally made difficult to read or analyze.

**Detection patterns:**
```
- Minified JavaScript (single-line, no whitespace, single-letter variables)
- Base64-encoded strings that are decoded and executed
- Hex-encoded payloads
- String concatenation to build commands: "cu" + "rl" + " htt" + "p://..."
- Unicode escape sequences for ASCII characters
- Compressed/packed scripts (eval(unescape(...)))
- ROT13 or custom encoding
```

**Severity:** 🔴 If code is executed after deobfuscation; 🟡 If obfuscation is build-artifact (e.g., webpack bundle)

**Key question:** Is there a legitimate reason for the obfuscation? Build tools produce minified output, but a hand-written skill should never be obfuscated.

---

## 10. Process / System Reconnaissance

**What:** Code inspects running processes, network state, or system configuration.

**Detection keywords:**
```
ps aux, ps -ef, pgrep, pidof,
/proc/PID/environ, /proc/PID/cmdline, /proc/PID/maps,
ss -tlnp, netstat, lsof -i, nmap,
cat /etc/os-release, uname -a, hostnamectl,
systemctl list-units, journalctl
```

**Severity:** 🟡 For basic system info; 🔴 For process environment leaking or network scanning

**False positive:** A monitoring skill checking `df -h` for disk usage is expected. Reading `/proc/PID/environ` is almost never legitimate.

**Real-world example:** PoC document: `cat /proc/$(pgrep -f openclaw-gateway)/environ | tr '\0' '\n'`

---

## 11. Browser Session / Cookie Access

**What:** Code accesses browser storage, cookies, or session data.

**Detection keywords:**
```
document.cookie, localStorage, sessionStorage, IndexedDB,
chrome.cookies, browser.cookies,
Cookie:, Set-Cookie:,
~/.config/google-chrome, ~/Library/Application Support/Google/Chrome,
~/.mozilla/firefox, ~/Library/Application Support/Firefox,
Login Data, Cookies (SQLite files)
```

**Severity:** 🔴 Always — browser session data is highly sensitive

**False positive:** A browser automation skill that needs cookie access for its stated function, with clear documentation. Still requires human approval.

---

## Usage Notes

1. **One red flag ≠ automatic rejection.** Context matters. A single `process.env.MY_SKILL_API_KEY` is different from `env | grep -i secret`.

2. **Combination amplifies risk.** Credential access + network send = much worse than either alone.

3. **Check the full execution path.** A function that reads credentials might not send them itself, but passes them to another function that does.

4. **Non-code files are attack surfaces too.** Always cross-reference with [social-engineering.md](social-engineering.md) for prompt injection in documentation files.
