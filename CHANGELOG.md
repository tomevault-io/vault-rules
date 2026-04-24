# Changelog

All notable changes to the vault-rules scan rule set are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and the project adheres to Semantic Versioning as described in the README.

## [1.0.0] — 2026-04-24

Initial public release. Rules extracted from the internal `scripts/security_scan.py` implementation that has been running against the TomeVault index since Stage A.

### Added

- **credential** — 15 rules covering API keys (Anthropic, OpenAI, GitHub, AWS, Stripe, Slack, Google, Supabase, Resend, Postmark), generic secrets, bearer tokens, and private key blocks.
- **command** — 14 rules covering destructive shell patterns (`rm -rf /`, `curl | sh`, `mkfs`, `dd` to devices, `iptables -F`, etc.).
- **exfiltration** — 8 rules covering secrets-to-URL patterns, reverse shells (netcat, socket+subprocess), base64-pipe-exec, and webhook exfil.
- **injection** — 10 rules covering prompt-injection patterns ("ignore previous instructions", "disregard prompt", "jailbreak", "enable developer mode", hidden HTML comments, etc.).
- **file_access** — 8 rules covering reads of `/etc/passwd`, SSH keys, `.env`, credentials files, `/proc`, shell history, Docker socket, and kube config.
- **obfuscation** — 5 rules covering base64 blobs, hex-encoded payloads, zero-width character runs, Unicode-confusable mixing, and URL-encoded schemes.

### Known gaps

- No coverage for tool-use-specific exfil patterns (agent-callable tools returning sensitive data). Covered by AST-07 in the AST10 vocabulary; rules to be added in a future minor release as the pattern base grows.
- No heuristics-layer rules here yet — heuristics run outside this repository for now and will be migrated when their shape stabilises.
