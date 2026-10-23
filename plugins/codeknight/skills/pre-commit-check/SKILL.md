---
name: pre-commit-check
description: Check staged changes with CodeKnight before committing. Use when the user asks to commit, before running git commit, or when asked to check a diff for secrets, dangerous calls, or other CodeKnight findings.
---

Check the changes that are about to be committed with this plugin's `codeknight_check_diff` tool (MCP name `mcp__plugin_codeknight_codeknight__codeknight_check_diff`).

1. Call the tool with no `diff`, so it reviews the staged git changes in the project. Pass `scope: "all"` only when the user wants unstaged tracked changes included. Pass `path` only to check a different checkout.
2. Read the JSON result. If `blocked` is true, do not run `git commit`. Tell the user each blocking finding's severity, file, line, title, and remediation, and what would clear the check.
3. If `blocked` is false, you may commit. Mention any findings that were reported but did not block.
4. Do not retry the commit by weakening the check, passing `failOn: "none"`, or skipping hooks, unless the user explicitly asks for that.
5. A `diff` argument reviews text only and never sends it to the CodeKnight server. Prefer the git check so the repository's `.codeknight.json` applies.

The plugin also runs a local check when a `git commit` command is about to run, and blocks the command when that result is blocked. That automatic check does not call the CodeKnight server. Calling the tool here is what runs optional AI review, and only when the plugin's API token and server URL are both set.
