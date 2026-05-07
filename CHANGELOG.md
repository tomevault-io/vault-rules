# Changelog

All notable changes to the vault-rules scan rule set are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and the project adheres to Semantic Versioning as described in the README.

## [1.1.0] — 2026-05-06

RFC-001 trivial-tier ratification. Authored by an LLM rule-set reviewer pass over the [DataDog `ai-skills` malicious corpus benchmark](https://tomevault.io/transparency/datadog-ai-skills-2026-05-06) (n=204, snapshot 2026-05-06), validated against a 1,000-file random sample of TomeVault production pass-graded files (per-rule false-positive rate ≤ 0.5%). Phase 2 (rules introducing new categories or first-of-kind behavioural patterns) is queued for vault-rules v1.2 — to ship under the public 7-day RFC comment window.

Benchmark impact: 24.0% → 39.2% true positive rate on the same corpus, precision unchanged (0 false positives on labeled-safe subset).

Full RFC text: `briefings/vault-rules-rfc-001-ai-skill-attacks.md` in the TomeVault main repo.

### Added — command (6 rules)

- **dynamic_child_process** (critical) — Dynamic import of `node:child_process` followed by `execSync(...)` within ~200 chars. JS-RCE pattern.
- **glot_io_install** (high) — Skill install instructions linking to `glot.io/snippets/`. Code-paste service abused as malware host.
- **pwd_protected_install** (medium) — Skill telling user to extract a downloaded zip with a literal password (e.g., `openclaw`, `infected`). Malware-delivery convention.
- **launchd_persist** (high) — macOS `LaunchAgents/.plist` install or `launchctl load` — persistent background-process installation.
- **shell_init_append** (high) — Appending to `.bashrc` / `.zshrc` / shell init files — persistence mechanism.
- **cron_install** (high) — `crontab -l/-e`, `/etc/cron.*`, `@reboot` — persistent scheduled execution.

### Added — exfiltration (2 rules)

- **curl_upload_system_file** (critical) — `curl` uploading a file from a system path (`/etc/`, `/proc/`, `/var/log/`, `~/.`). Direct exfiltration vector.
- **dotenv_then_post** (high) — Reading `.env` contents followed by network POST within 500 chars. Composite pattern.

### Added — file_access (3 rules)

Broader path-mention rules (no read/cat verb required) catch attempts that don't use explicit read verbs.

- **ssh_key_path_ref** (high) — `.ssh/{id_rsa, id_ed25519, id_dsa, authorized_keys, known_hosts}` path mention.
- **aws_creds_path_ref** (high) — `.aws/{credentials, config}` path mention.
- **passwd_path_ref** (critical) — `/etc/{passwd, shadow, sudoers}` path mention (broader than the existing verb-required rule).

### Added — obfuscation (3 rules)

- **b64_pipe_shell_payload** (critical) — base64-encoded shell command piped into a shell interpreter. Universally suspicious; closes the largest single attack vector in the DataDog corpus.
- **b64decode_then_exec** (critical) — `base64.b64decode(...)` followed by `exec(...)` within 200 chars. Encoded-payload execution.
- **compile_exec_string** (critical) — Python `compile(source, '<string>', 'exec')` — dynamic-string compilation as a precursor to `exec(compiled)`.

### Deferred to v1.2 (production-FP-flagged in RFC-001 review)

- `curl_pipe_any_shell` — broad pattern matched legitimate first-party installers. Will return in v1.2 with an allowlist of known-good installer hosts.
- `exfil_send_seed_phrase` — matched skills *warning against* sharing private keys. Will return in v1.2 with imperative-voice tightening.

### Queued for vault-rules v1.2 (Phase 2)

Phase 2 introduces new categories and first-of-kind behavioural patterns. It will ship under the public 7-day RFC comment window described at [tomevault.io/standards/ratification](https://tomevault.io/standards/ratification).

- `self_labelled_malicious` (high, new category `self_disclosure`) — 8 catches in benchmark
- `hidden_from_user` (high, social_engineering) — first behavioural-language rule
- `imperative_must_execute` (medium, social_engineering) — broader FP review needed
- `autonomous_trading_bot` (medium, new category `financial_risk`) — 10 catches; flag-vs-block policy decision pending
- `webhook_with_system_file` (critical, exfiltration composite)
- `gh_token_with_network` (high, exfiltration composite)

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
