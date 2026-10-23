# CodeKnight for Claude Code

CodeKnight adds one tool to Claude Code, `codeknight_check_diff`, and a `/codeknight:review` skill. Claude can call the tool to check its own changes before it commits. The rule-based checks run on your machine. They need no account and make no network calls. By default Claude itself does the AI review, on your Claude subscription. Nothing is sent to a model API or to a CodeKnight server unless you explicitly ask for a server review.

## Install

In Claude Code:

```
/plugin marketplace add BOEA-TNMF-GROUP/codeknight-claude-plugins
/plugin install codeknight@codejackass
```

![CodeKnight](./icon.png)

## What the tool checks

With no arguments, the tool checks the staged changes (`git diff --cached`) in the project directory. You can also ask it to check:

- `scope: "all"`: staged and unstaged changes to tracked files together (new files are checked once staged)
- `path`: another directory inside a git repository
- `diff`: a unified diff passed as text, up to 2 MiB, instead of the working tree

The local rule-based checks are the same ones the CodeKnight CLI and editor extension run:

- secrets and key material in added lines, and logging of credential values
- dangerous calls and patterns, such as `eval()`, shell injection, SQL built by string concatenation, `innerHTML`, disabled TLS verification, permissive CORS, JWT verification bypass and insecure randomness
- request handling: path traversal, a tenant id taken from the request, unchecked `fetch`, Stripe events without dedupe, and a route missing its sibling's auth check
- unsafe database migrations, such as destructive drops, unbounded deletes, renames and blocking index builds
- unpinned or non-registry dependency specs
- added text that tries to instruct AI reviewers or coding agents
- change heuristics, such as large changes and missing tests

When the tool checks git changes, the repository's `.codeknight.json` is applied the same way the CLI applies it. It can only make the checks stricter.

The tool returns JSON: the findings (severity, category, file, line, title, description and remediation), whether the change is blocked, and notes. By default a change is blocked when a finding is `critical`. Pass `failOn` to use a different severity.

## Before a commit

The `pre-commit-check` skill (`/codeknight:pre-commit-check`) tells Claude to call `codeknight_check_diff` before it commits. That call returns the local findings and the brief for Claude's own review (see AI review below).

The plugin also registers a hook. When Claude is about to run `git commit` in Bash or PowerShell, the hook reviews the staged changes with the local rule-based checks and blocks the command if a finding meets the gate. The hook does not call the CodeKnight server and does not use the API token. It needs `node` and `git` on your `PATH`, and it runs the plugin's bundled server (`server/codeknight-mcp.js`).

Commits made outside Claude Code are not seen by this hook. `codeknight hook install` adds a git pre-commit hook for those.

## AI review

By default the tool returns the local findings and a fixed brief (`aiReview` is `customer-agent`). Claude follows that brief and reviews the same changes. That review uses your Claude subscription. CodeKnight does not call a model and does not spend a CodeKnight key.

Pass `"ai": false` for the local findings only.

A CodeKnight server review runs only when all of these are true:

- the call passes `"ai": true` (ask for this in words; the skill does not pass it on its own)
- you set both the API token and the server URL in the plugin's settings
- the tool is checking git changes, not a `diff` passed as text
- there are changes to check

The API token is the client token that `codeknight login` issues. A server review also needs a CodeKnight plan; see [pricing](https://codejackass.com/pricing). The repository's GitHub owner must match an installation of the CodeKnight GitHub App that the token can see. If the server review can't run, the tool still returns the local findings and Claude reviews them.

The plugin never calls an AI provider itself. It does not use a provider API key from your environment. The server path is the only one that leaves the machine, and only after you ask for it.

## What leaves your machine

**By default, nothing does.** The plugin reads the repository with `git` and runs the rule-based checks locally. Claude reviews the result in the session you already have open.

**When you ask for a server review and a token and server URL are set**, that review sends these to the server URL you set, over HTTPS:

1. `GET /cli/whoami`, with your token. This finds the CodeKnight installation for the repository's owner.
2. `POST /api/v1/review`, with your token, the repository name from the `origin` remote, the commit SHA, the repository's `.codeknight.json` settings, and the changed files: paths, line counts and diffs.

Before the upload, the plugin applies the same steps the CLI and editor extension apply:

- Credential files, such as `.env`, `*.pem`, `*.key`, `id_rsa`, `.npmrc` and `credentials.json`, are withheld. Only their path and line counts are sent.
- Secrets in the diff text of every other file are replaced with a redaction marker.

The plugin itself does not send telemetry, and it does not contact codejackass.com unless that is the server URL you set. How the CodeKnight server handles uploaded changes is covered by the [privacy policy](https://codejackass.com/privacy).

## Settings

| Setting | Used for |
| - | - |
| CodeKnight API token | Server review only, and only when you ask for it. Stored by Claude Code as a sensitive value. |
| CodeKnight server URL | Server review only, and only when you ask for it. Must be `https://` (or `http://localhost` for local testing). |

The MCP server runs with `node`, so Node.js must be on your `PATH`. It has been tested with Node.js 22. Checking git changes also needs `git`.

## Support and terms

- Support: support@codejackass.com
- [Terms](https://codejackass.com/terms)
- [Privacy policy](https://codejackass.com/privacy)
