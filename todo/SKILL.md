---
name: todo
description: Use this when the user wants to pick up, refine, or triage the Agent's Jira backlog — invoked as /todo, or from phrases like "what should I work on next", "let's refine a jira ticket", "get this ticket ready", "clear out my to-do list", or "pick something off the backlog". Finds subtasks assigned to the Agent Jira account sitting in a not-yet-ready status, auto-advances any that are already sufficiently defined straight to Ready, then aligns with the user on which of the remaining subtasks to refine — working it against the team's Definition of Ready (fetched live from Confluence, never hardcoded) until it too can move to Ready, blocking with an owner for anything that can't be resolved in conversation. Always prefer this over ad-hoc Jira queries when the user's intent is "help me get something ready to work on."
---

# /todo — Jira backlog triage

Take Jira subtasks from "not ready" to "Ready," using a Definition of Ready that lives in Confluence (or wherever the team keeps it) rather than baked into this file. The DoR is a living team document — treat it as the source of truth every time, not something to cache or assume from a past run.

Use the `mcp__claude_ai_Atlassian_Rovo__*` tools for all Jira/Confluence access. Don't use subagents for this — it's a conversational, iterative workflow and the user needs to be in the loop at each mutation that touches something outside what step 2 below auto-clears.

## 1. Resolve the Agent account

This team's convention: parent tickets (epics, stories, larger tasks) are never assigned directly to an agent — only subtasks are, and only subtasks assigned to the **Agent** Jira account (`will.h.bishop+agent@gmail.com`) are this skill's business. That's a distinct Jira user from whoever is authenticated to the MCP connection, so don't use `currentUser()` for the query.

Resolve it once with `lookupJiraAccountId` (email `will.h.bishop+agent@gmail.com`) and reuse that account ID for the rest of this run.

## 2. Fetch the Definition of Ready

Find it in Confluence, don't hardcode it — you need this before you can judge anything below:

1. Search for a page titled "Definition of Ready" (or close to it), scoped to the relevant Jira project's space first, then fall back to a team-wide or global space if there's no project-specific one.
2. If more than one plausible candidate exists, ask the user which one governs rather than picking silently.
3. If none exists at all, tell the user and ask whether to create one — don't invent criteria yourself and proceed. The whole point is that this checklist belongs to the team, not to this skill.

Read the actual checklist content fresh each run; don't rely on anything remembered from a previous invocation.

## 3. Find candidate subtasks

Query:

```
assignee = "<agent-account-id>" AND issuetype = Subtask AND statusCategory = "To Do" ORDER BY updated DESC
```

**Don't assume `statusCategory = "To Do"` means "not started."** Some workflows put more than one named status in that category — for example a team might have both `To-Do` and `Ready` both categorized as "To Do." Work out which status name in each project actually represents "not yet ready" versus the target "Ready" status (check the distinct status names in the results, and what `getTransitionsForJiraIssue` offers from each), and exclude anything already sitting in the Ready-equivalent status.

If nothing comes back, say so plainly and stop.

## 4. Auto-advance anything already sufficiently defined

Before involving the user, sweep the candidates: for each one, check every DoR criterion against the **subtask's own fields plus its parent ticket's description/fields** — the parent is legitimate context a subtask doesn't need to duplicate. If a subtask genuinely satisfies every criterion this way already, move it straight to Ready (find the transition via `getTransitionsForJiraIssue`, run it with `transitionJiraIssue`) without waiting for a one-by-one conversation — that's the point of this pass, to clear the easy ones automatically.

Report what you moved and why (which criteria were satisfied and where — subtask or parent — before continuing), so the user can catch a bad call. Don't ask permission before each individual transition in this sweep, but don't hide it either.

Anything left after this pass has a genuine gap — that's what step 5 onward is for.

## 5. Align with the user

Present the remaining candidates (key, summary, parent, age, and which DoR criteria are unmet) and ask which one to work on next. Don't guess based on priority or recency — this is the user's call. If they want more context on one, pull its full description/comments/parent before they decide.

## 6. Work the subtask against the checklist

For each unmet criterion, try to close it **in conversation first** — ask the user directly, propose wording, update the subtask's description/fields once they confirm. Most gaps are small and close this way.

If a gap genuinely can't be closed in conversation (needs someone else's decision, external access, research, setup work), don't leave it as vague ambiguity and don't invent a fix. Jira subtasks can't have their own subtasks, so you can't spin off further work under it the way a parent ticket could — instead:

1. Move the subtask to a blocked status if one exists in this project's workflow (check `getTransitionsForJiraIssue`); if there's no explicit blocked status, say so and add a clear comment flagging it instead of inventing a status that doesn't exist.
2. Comment on the subtask (and its parent, if the gap is really something the parent should have specified) describing exactly what's missing and who needs to weigh in.
3. Ask the user who should own resolving it, or infer a reasonable owner and confirm with them before assigning/notifying.

(If the user explicitly asks you to work a parent-level ticket directly instead of a subtask, the organic-subtask pattern applies there: for a gap you can't close in conversation, create a subtask describing that one piece, get an owner, and move the parent to blocked with that owner — the parent only ever moves forward, never backward once past a status.)

## 7. Transition to Ready

Once every checklist item is genuinely met (not "close enough" — confirm each one explicitly with the user before the final transition), find the transition to the Ready-equivalent status via `getTransitionsForJiraIssue` and run it with `transitionJiraIssue`.

Show the user the final checklist state and get their go-ahead before firing this one — unlike the automatic sweep in step 4, this subtask needed real back-and-forth to get here, so confirm before the last mutation.

## Notes

- Field edits, comments, and blocked-transitions are real actions on shared Jira/Confluence data other people may see. Confirm with the user before each one, except for the step 4 auto-advance sweep, which is meant to run without per-item confirmation — just report it clearly.
- If the user only wants to browse or discuss candidates without committing to refine one yet, that's fine — steps 1-5 stand alone.
