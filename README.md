# Audit Setup

A Claude Code skill that reviews your configuration against the latest Claude Code documentation and recommends improvements.

Run it periodically (e.g. monthly) to catch new features that could improve your workflow.

## What it does

1. **Reads your current setup** — CLAUDE.md, hooks, MCP servers, skills, agents, settings, permissions
2. **Fetches the latest Claude Code documentation** — new features, deprecations, security changes
3. **Runs a gap analysis** — what you have vs what is available
4. **Presents recommendations** in five categories: Adopt, Improve, Remove, Security, Parked
5. **Offers to implement** approved changes with per-item diffs

## Security

This skill was red-teamed before release. Key protections:

- **Never reads or outputs secret values.** Environment variable names only. API keys, tokens, and credentials are never accessed.
- **Fetched documentation is treated as untrusted data.** Prompt injection attempts in external sources are ignored and flagged.
- **Output is reviewed for sensitive information** before presenting. File paths are redacted, hook script contents are described rather than shown.
- **Implementation requires per-item confirmation.** No batch changes without individual review, even when applying a category.
- **No third-party dependencies.** Pure markdown. No executable code. No external tools installed.

## Installation

Copy `audit-setup.md` to your Claude Code commands directory:

```bash
cp audit-setup.md .claude/commands/audit-setup.md
```

Then run it with:

```
/audit-setup
```

## Requirements

- Claude Code (any recent version)
- Web search or web fetch capability (for Step 2)

## Licence

MIT
