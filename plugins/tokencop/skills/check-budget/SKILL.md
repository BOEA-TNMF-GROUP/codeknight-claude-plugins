---
name: check-budget
description: Check a TokenCop budget before a long or costly run, and ask a TokenCop admin for more when it is short. Use when the user mentions TokenCop, a task budget, or token spend limits, or before starting work on a TokenCop task.
---

Use this plugin's TokenCop tools (MCP names `mcp__plugin_tokencop_tokencop__tokencop_check_budget` and `mcp__plugin_tokencop_tokencop__tokencop_request_budget`).

1. Before a long run on a TokenCop task, call `tokencop_check_budget` with the task's `taskId`. Without a task id, it reports the account's plan status, its usage this month and its ceilings.
2. Tell the user how much budget is left. If the work will clearly need more, say so before starting.
3. Call `tokencop_request_budget` only when the budget is short and the user agrees. Give the new total budget for the task in `newBudgetTokens`, and a short, plain `reason`.
4. Never put a secret, key, password or customer data in `reason`. Admins read it in the dashboard.
5. A request is pending until a TokenCop admin approves it. Do not treat it as approved, and do not keep working past the budget while it is pending. Check again with `tokencop_check_budget` later.
6. If a tool returns an error saying the API key or server URL is not set, tell the user to set them in the plugin's settings. Do not ask them to paste the key into the chat.
