# Proposing a rule change (RFC)

Changes to the vault-rules scan ruleset pass through a public RFC process governed by the TomeVault Policy Council ([tomevault.io/policy/council](https://tomevault.io/policy/council)).

## Who can propose

Anyone. Open an issue in this repository using the `RFC` label.

## Template

Copy the following into your issue:

```markdown
## Summary

One sentence describing the proposed change.

## Rule change

- **Type:** add / modify / remove
- **Target rule ID(s):** [e.g., credential.stripe_key, or N/A for additions]
- **Proposed pattern:** [regex, with flags]
- **Proposed severity:** critical | high | medium | low
- **AST10 mapping:** [e.g., AST-06, AST-10]

## Motivation

Why is this change needed? Include:
- Concrete false-positive or false-negative examples from the current ruleset.
- The risk the new rule catches or the risk the removed rule was meant to catch.
- Any upstream reference (OWASP advisory, CVE, security research).

## Backwards compatibility

Will this change reclassify existing indexed files across grade boundaries?
If yes, estimate the impact and suggest a migration approach.

## Test cases

Provide at least:
- 3 positive cases (content that SHOULD match)
- 3 negative cases (content that should NOT match — common false positives)

## Alternatives considered

Briefly describe at least one alternative approach and why you rejected it.
```

## Process

1. RFC stays open a minimum of 14 days for public comment.
2. The Policy Council reviews at the next convened session (triggered by queue size or incident; see the Council page).
3. Council ratification requires a two-of-three vote.
4. Ratified changes land as a tagged release. The TomeVault scan pipeline picks up the latest ratified release automatically on the next scan cycle; re-scoring of affected files happens on the rolling monthly rescan.

## Time-critical fixes

An incident involving the production scanner (false-positive storm on a high-volume class of files, missed detection on a disclosed CVE class) may ship under a 7-day ratify-after clause. The change is applied immediately, an incident log entry is opened at [tomevault.io/transparency](https://tomevault.io/transparency), and the Council ratifies within 7 days or the change is rolled back.

## Questions

Email [oli@tomevault.io](mailto:oli@tomevault.io) or open an issue on this repository.
