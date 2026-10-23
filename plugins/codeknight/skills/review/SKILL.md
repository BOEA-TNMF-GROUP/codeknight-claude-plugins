---
name: review
description: Review code changes with CodeKnight before a commit. Use when the user asks to review a diff, staged changes, or whether a change is safe to commit. Rule checks run on this machine; you do the AI review yourself.
---

# CodeKnight review

1. Call the `codeknight_check_diff` tool on the changes. Do not pass `ai: true` unless the user explicitly asks for a CodeKnight server review.
2. Treat the tool's local findings as already decided. They ran on this machine and made no model call.
3. When `aiReview` is `customer-agent`, you are the reviewer. Follow `agentBrief` in the tool result, using the changes you can already see. Do not call a model API and do not send the code to a server.
4. When `aiReview` is `completed`, the server already reviewed. Report those findings and do not send the change to another model.
5. When `aiReview` starts with `unavailable`, say why, then review the changes yourself. Do not retry the server.
6. State each defect with the file, the line and the concrete failure. If nothing is wrong, say so. `blocked` covers the local rules only.
