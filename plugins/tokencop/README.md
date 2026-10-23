# TokenCop for Claude Code

TokenCop is a budget guard for AI agents. This plugin gives Claude Code two tools for an existing TokenCop account: one checks the remaining budget, and one asks a TokenCop admin for more. The plugin needs a TokenCop API key and the URL of your TokenCop server. Without them, both tools return an error that says what to set.

## Install

In Claude Code:

```
/plugin marketplace add BOEA-TNMF-GROUP/codeknight-claude-plugins
/plugin install tokencop@codejackass
```

![TokenCop](./icon.png)

## Tools

### `tokencop_check_budget`

- With `taskId`: returns that task's budget, its spend so far, and the tokens left for the task, the month and the day.
- Without `taskId`: returns your account's plan status, its usage so far this month, and its monthly and daily ceilings.

### `tokencop_request_budget`

Takes `taskId`, `newBudgetTokens` (the new total budget for the task) and `reason`. It records a pending budget request with the reason. A TokenCop dashboard admin then approves or rejects it. The tool does not raise the budget by itself. If the task already has a pending request for its current budget round, the tool returns that request and does not create a second one.

Task ids come from your TokenCop integration (the TokenCop API or SDK) or from the TokenCop dashboard. This plugin does not create tasks, reserve tokens or report usage.

After the free trial, TokenCop needs a paid plan for task calls; see [pricing](https://codejackass.com/pricing). The account check works either way.

## What leaves your machine

Only when Claude calls one of the tools, the plugin sends one HTTPS request to the server URL you set:

- `tokencop_check_budget`: `GET /api/tokencop/v1/me` or `GET /api/tokencop/v1/tasks/<taskId>`
- `tokencop_request_budget`: `POST /api/tokencop/v1/tasks/<taskId>/budget-requests` with `newBudgetTokens` and `reason`

Each request carries your API key in the `Authorization` header. The plugin reads no files and sends no code. It does not send telemetry, and it does not contact any other server. How the TokenCop server handles your data is covered by the [privacy policy](https://codejackass.com/privacy).

## Settings

| Setting | Used for |
| - | - |
| TokenCop API key | Every call. An API key from the TokenCop dashboard. Stored by Claude Code as a sensitive value. |
| TokenCop server URL | Every call. Must be `https://` (or `http://localhost` for local testing). |

The MCP server runs with `node`, so Node.js must be on your `PATH`. It has been tested with Node.js 22.

## Support and terms

- Support: support@codejackass.com
- [Terms](https://codejackass.com/terms)
- [Privacy policy](https://codejackass.com/privacy)
