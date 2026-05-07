# vault-rules

Public, version-controlled scan rules that TomeVault runs against indexed AI instruction files.

## Why this repository exists

TomeVault scans every indexed file (CLAUDE.md, AGENTS.md, GEMINI.md, SKILL.md, Cursor rules, Copilot instructions, Windsurf rules, and related formats) for security, prompt-injection, and data-exfiltration risks. The rules that drive those scans live here so anyone can audit exactly what the scanner looks for, propose changes, and watch the rule set evolve.

Full scanning policy: [tomevault.io/policy/security-scanning](https://tomevault.io/policy/security-scanning)

## Layout

```
v1/
  credential.yaml     — API keys, tokens, private keys
  command.yaml        — destructive shell patterns, privilege escalation
  exfiltration.yaml   — secrets-to-URL, reverse shells
  injection.yaml      — prompt-injection patterns (jailbreak, override, etc.)
  file_access.yaml    — /etc/passwd, SSH keys, /proc, docker socket
  obfuscation.yaml    — base64 blobs, zero-width chars, encoded payloads
```

Each rule has:

- `id`: stable identifier used in scan results and creator dashboards.
- `severity`: one of `critical`, `high`, `medium`, `low`.
- `pattern`: Python-style regex.
- `flags`: optional list, currently only `IGNORECASE`.

The AST10 category mapping appears at the top of each YAML file.

## Proposing a change

Open an issue using the RFC template (see [`RFC.md`](RFC.md)). Changes ratify per the process described at [tomevault.io/standards/ratification](https://tomevault.io/standards/ratification).

Ratified changes land as tagged releases (`v1.0.0`, `v1.1.0`, ...). Scanners pick up the latest ratified release automatically on the next scan cycle.

## Versioning

Semantic versioning, scoped to behavior change:

- **Major** — a change that would reclassify existing indexed files across grade boundaries (e.g., demoting a rule from `critical` to `medium`, adding a wide-reaching rule).
- **Minor** — additive rules or clarifications that do not move existing files across grades.
- **Patch** — documentation, pattern tightening that reduces false positives without adding coverage.

## License

MIT. Rules may be used and adapted by anyone.
