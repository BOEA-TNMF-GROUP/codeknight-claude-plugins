# CodeKnight and TokenCop plugins for Claude Code

This repository holds two Claude Code plugins published by codejackass:

- [`plugins/codeknight`](plugins/codeknight/README.md): lets Claude check its staged changes, or a diff, with CodeKnight before it commits. The rule-based checks run on your machine with no account. By default Claude does the AI review itself, on your Claude subscription. A CodeKnight server review is optional.
- [`plugins/tokencop`](plugins/tokencop/README.md): lets Claude check a TokenCop budget and ask a TokenCop admin for more. Needs a TokenCop account.

Each plugin's README says what it does and what leaves your machine.

## Install

In Claude Code:

```
/plugin marketplace add BOEA-TNMF-GROUP/codeknight-claude-plugins
/plugin install codeknight@codejackass
/plugin install tokencop@codejackass
```

Each plugin runs a small MCP server with `node` (the file under `plugins/<name>/server/`). It is built from CodeKnight's source and is not meant to be edited here.

## Links

- Website and pricing: https://codejackass.com/pricing
- Terms: https://codejackass.com/terms
- Privacy: https://codejackass.com/privacy
- Support: support@codejackass.com

These plugins are proprietary software of Blue Ocean Enterprises Australia Pty Ltd. See [LICENSE](LICENSE).
