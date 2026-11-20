---
id: high-signal-review
name: High-signal review policy
# Optional extra (commented out in the V2 config). Set to true to turn it on.
enabled: false
paths:
  - "**/*"
---

- Flag any unintentional behavior changes in this codebase or any linked repositories
- Review correctness, security, reliability, performance regressions, and functionality-breaking defects
- Avoid posting any refactoring suggestions and keep comments brief, actionable, and limited to blocking or high-confidence issues
- Avoid style, formatting, naming, documentation, and subjective refactoring unless they directly cause a bug or materially increase risk and when uncertain, prefer no comment over speculative feedback
