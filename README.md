# TeamPCP / Shai-Hulud YARA Rules
### Open Source Defense — Breaking Circuits LLC

Public YARA detection rules for the **Shai-Hulud / Mini Shai-Hulud** supply chain worm 
campaign operated by threat actor group **TeamPCP** (aliases: DeadCatx3, PCPcat, 
ShellForce, CipherForce).

**CVE-2026-45321 | CVSS 9.6 Critical | GHSA-g7cv-rxg3-hmpx**

---

## What This Detects

- Primary campaign beacon strings (GitHub dead-drop commit messages)
- Deadman switch persistence artifacts (`gh-token-monitor`)
- PBKDF2 campaign salt (`svksjrhjkcejg`) — unique malware fingerprint
- PyPI second-stage persistence (`pgsql-monitor`)
- C2 infrastructure indicators
- Worm propagation markers

---

## Threat Actor Profile

| Attribute | Value |
|-----------|-------|
| Group | TeamPCP |
| Aliases | DeadCatx3, PCPcat, ShellForce, CipherForce |
| Active since | March 2026 |
| Campaign | Shai-Hulud: Here We Go Again |
| Ecosystems | npm, PyPI, GitHub |
| Prior targets | LiteLLM, Aqua Trivy, Bitwarden CLI, SAP CAP, TanStack, Mistral AI, UiPath, OpenSearch, Guardrails AI |

---

## Quick Scan

```bash
# Scan a directory
yara teampcp_shai_hulud.yar /path/to/scan

# Scan npm cache
yara teampcp_shai_hulud.yar ~/.npm

# Scan Claude Code and VS Code dirs
yara teampcp_shai_hulud.yar ~/.claude ~/.vscode

# Scan node_modules recursively
yara -r teampcp_shai_hulud.yar ./node_modules
```

## ⚠️ Critical Warning

**DO NOT revoke GitHub tokens on a potentially infected machine before 
neutralizing the deadman switch.** Token revocation triggers `rm -rf ~/` 
via a background polling daemon.

Neutralize first:
```bash
# Linux
systemctl --user stop gh-token-monitor.service
systemctl --user disable gh-token-monitor.service
rm -rf ~/.local/bin/gh-token-monitor.sh ~/.config/gh-token-monitor/

# macOS
launchctl bootout "gui/$(id -u)" \
  ~/Library/LaunchAgents/com.user.gh-token-monitor.plist
rm ~/Library/LaunchAgents/com.user.gh-token-monitor.plist
rm -rf ~/.local/bin/gh-token-monitor.sh ~/.config/gh-token-monitor/
```

Then rotate all credentials.

---

## Related Resources

- Full IoC list and threat report: [breakingcircuits.com](https://breakingcircuits.com)
- CVE: [CVE-2026-45321](https://nvd.nist.gov/vuln/detail/CVE-2026-45321)
- GHSA: [GHSA-g7cv-rxg3-hmpx](https://github.com/advisories/GHSA-g7cv-rxg3-hmpx)
- JFrog Research: [Shai-Hulud: Here We Go Again](https://jfrog.com/blog/shai-hulud-here-we-go-again)
- TanStack Postmortem: [tanstack.com/blog](https://tanstack.com/blog/npm-supply-chain-compromise-postmortem)
- Disclosed via HackerOne: [hackerone.com/breakingcircuit](https://hackerone.com/breakingcircuit)

---

*Breaking Circuits LLC — Securing municipalities and critical infrastructure 
through AI and open source. Pulaski, VA.*
