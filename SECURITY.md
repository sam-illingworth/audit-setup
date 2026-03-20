# Security Assessment

This skill was red-teamed before release using a structured security review methodology. Below is a summary of the findings.

## Threat model

This is a markdown instruction file, not executable code. The primary risks are:

1. **Information disclosure** — the skill reads sensitive configuration files and could expose their contents in the output report
2. **Prompt injection** — the skill fetches external documentation that could contain malicious instructions
3. **Unintended configuration changes** — the skill offers to implement recommendations, which could be influenced by poisoned external content

## Findings

Five findings were identified. All were remediated before release.

### Finding 1: Environment variable values could appear in output (High)

The skill catalogues environment variables as part of the setup inventory. If values were included, API keys and tokens could leak into the report.

**Mitigation:** The skill explicitly instructs Claude to record environment variable names only, never values. Variables matching secret patterns (TOKEN, KEY, SECRET, PASSWORD, CREDENTIALS, API_KEY) are flagged as existing but never read.

### Finding 2: Fetched documentation could contain prompt injection (High)

Step 1 loads sensitive configuration into the context window. Step 2 then fetches external web content into the same context. If a fetched page contained prompt injection, it would execute with full knowledge of the user's configuration.

**Mitigation:** The skill includes a hostile content rule instructing Claude to treat all fetched content as untrusted data, ignore any instructions found in external sources, and flag potentially compromised sources. The skill also pins trusted sources to official Anthropic domains and flags unofficial sources explicitly.

### Finding 3: Output could contain sensitive configuration details (Medium)

The audit report describes the user's setup in detail. If shared publicly (e.g. in a screenshot or blog post), it could expose hook logic, permission rules, or MCP server configurations.

**Mitigation:** The skill includes a redaction checklist applied before presenting the report: usernames in file paths are replaced, API endpoints and webhook URLs are removed, and hook script contents are described rather than shown. A disclaimer banner is included in the report template.

### Finding 4: Batch implementation without individual review (Medium)

The skill originally offered "all adopt" / "all improve" / "all remove" as batch options. A poisoned recommendation from Step 2 could be applied without scrutiny.

**Mitigation:** Even when applying a category in batch, the skill now shows each individual change and waits for explicit confirmation before proceeding to the next. No changes are applied without per-item review.

### Finding 5: No integrity check on documentation sources (Low)

The skill fetches documentation via web search without specifying trusted sources. Claude could fetch from unofficial mirrors or spoofed documentation.

**Mitigation:** The skill now pins preferred sources to official Anthropic domains (docs.anthropic.com, claude.ai/docs, github.com/anthropics) and flags any non-Anthropic sources as unofficial in the recommendation.

## Residual risk

The skill runs inside the user's Claude Code session with the same permissions as any other skill. It cannot access anything the user has not already granted access to. The primary residual risk is that a sophisticated prompt injection in fetched documentation could influence recommendations in subtle ways that pass human review. The per-item confirmation requirement is the final safeguard against this.

## Reporting issues

If you find a security issue with this skill, please open an issue on this repository or contact the author directly.
