# Audit Setup

A Claude Code skill that reviews your configuration against the latest Claude Code documentation and recommends improvements.

Run it periodically (e.g. monthly) to catch new features that could improve your workflow. If you are just getting started with Claude Code, this skill will show you which features are worth setting up first.

## What it does

1. **Reads your current setup** — CLAUDE.md, hooks, MCP servers, skills, agents, settings, permissions
2. **Fetches the latest Claude Code documentation** — new features, deprecations, security changes
3. **Runs a gap analysis** — what you have vs what is available
4. **Presents recommendations** in five categories: Adopt, Improve, Remove, Security, Parked
5. **Offers to implement** approved changes with per-item diffs

## Example output

```
## Claude Code Setup Audit — 2026-03-20

> This report contains information about your Claude Code configuration.
> Review before sharing publicly.

### Adopt (new features worth adding)

1. **Effort frontmatter in skills**
   Skills can now include an `effort` field in their frontmatter to override
   the session effort level. Your writing skill runs at default effort but
   would benefit from `effort: high` for better draft quality.
   → Add `effort: high` to the frontmatter of .claude/commands/writing.md

2. **StopFailure hook**
   New hook event fires when an API error occurs. You could auto-save session
   state on failure to prevent lost work.
   → Add a StopFailure hook to ~/.claude/settings.json that runs your
     backup script.

### Improve (existing config that could be better)

3. **Hook matchers too broad**
   Your PreToolUse hook runs on every tool call. Add a matcher to restrict it
   to specific tools (e.g. Read calls to sensitive directories only).
   → Add "matcher": "Read" to narrow the hook scope.

### Remove (deprecated or redundant config)

4. **npm-based installation**
   Your shell alias points to an npm-installed binary. Native installation
   is now recommended and faster.
   → Run: curl -fsSL https://claude.ai/install.sh | sh
     Then remove the npm alias from ~/.zshrc

### Security (hardening opportunities)

5. **MCP servers lack sandboxing**
   Your custom MCP servers run without network isolation. New sandboxing
   options restrict outbound access.
   → Add "sandbox": true to each server entry in .mcp.json

### Parked (interesting but not worth the effort right now)

6. **Agent teams**
   Experimental feature for multi-agent coordination. Powerful but high
   token cost and not stable enough for production workflows yet.
```

*The above is illustrative. Your actual report will reflect your specific setup.*

## Limitations

This skill does not:

- **Audit your custom code.** It reviews Claude Code configuration, not the quality of your skills, hooks scripts, or agent definitions.
- **Test hooks for bugs.** It checks whether hooks exist and whether newer hook types are available. It does not execute or validate them.
- **Benchmark performance.** It recommends features that may improve efficiency but does not measure actual token usage or response times.
- **Enforce team standards.** It audits individual setups against Claude Code's latest documentation, not against a team baseline. For team-wide configuration standards, consider adding a shared CLAUDE.md to your repo and using this skill to check each developer's local config against the latest features.

## Security

This skill was red-teamed before release. See [SECURITY.md](SECURITY.md) for the full assessment.

Key protections:

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
