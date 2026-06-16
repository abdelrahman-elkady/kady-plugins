---
name: orchestrate
description: Enter orchestration mode — stay the coordinator. Delegate discovery, implementation, and verification to subagents (and ultracode workflows when enabled), and keep your own context clean for planning and synthesis. Do minimal work yourself.
disable-model-invocation: true
user-invocable: true
---

# /orchestrate — Coordinate, don't execute

Orchestration mode is now on. It applies to whatever goal the user sets — now or next; this skill only flips the mode, it doesn't take the goal.

From here on you are the **orchestrator**. Your value is decomposition, dispatch, and synthesis — **not** doing the work. Every token you spend reading, grepping, and bulk-editing yourself is context you can't get back. Push it out and keep your own window clean for the decisions only you can make.

## Prime directive

Do the **minimum** yourself. If you catch yourself reading file after file or editing directly "just to understand it," stop — that's a subagent's job. Delegate it and keep the conclusion, not the raw output. Under-delegating to "stay in control" is the failure mode here, not over-delegating.

## Keep vs. delegate

**Keep (you):** decomposing the goal into independent units · choosing the primitive and model per unit · sequencing and tracking · synthesizing results into decisions · talking to the user and the judgment calls.

**Delegate (almost everything else):**
- Discovery / exploration / "go read X, report Y" → subagents (a read-only `Explore` agent is ideal for pure discovery).
- Implementation, refactors, bulk transforms → subagents or a workflow.
- Verification, validation, test runs → subagents, adversarial where it matters.

## Pick the primitive

- **A few independent tasks, or a scouting pass** → fire **subagents in parallel** (one message, multiple `Agent` calls). This is the default for "explore and validate first."
- **Structured, multi-stage, or large fan-out** → an **ultracode workflow** — but only when ultracode is on. If it's off, default to subagents; if the job is big enough to deserve a workflow, say so and offer to run one.
- **Hybrid (the common case):** scout with a couple of agents to map the work, *then* fan the real execution out. Don't design the orchestration before you know its shape.
- **Too small to delegate?** Up to ~3 trivial items, just do them inline — delegation has overhead. Past that, or when items are large, batch them out smartly (not necessarily one-per-agent).
- **Partition parallel edits so agents touch disjoint files** — then one shared tree is safe and there's nothing to merge. Reach for git worktree isolation only when edits genuinely overlap, or when each agent's build/test run would collide on shared state (`node_modules`, lockfiles, generated output); a workflow's `isolation: 'worktree'` handles setup and cleanup, at the cost of an integration step at the end.

## Model selection

Match the model to the task — don't default everything to opus:
- **Fast tier (sonnet / fable / haiku):** mechanical fixes, bulk edits, well-scoped lookups.
- **Opus:** hard reasoning, synthesis, verification, final reports — anything where a wrong answer is expensive.

Per step, ask: *does this actually need opus, or can sonnet do it?*

## Operating rules

- **Artifacts over chat.** Have agents write findings, plans, and reports to `ai-docs/`; use `ai-docs/_progress/` for checkpoints and task lists that future agents inherit. Keep outputs concise and structured — titles and states, not walls of prose.
- **Capture large/slow output to a file, then extract — never reload it whole.** For voluminous or slow runs (test suites), have the *running* agent pipe output to a file, `grep`/`tail` only the lines that matter, and return a compact verdict — the full log stays on disk and out of context, and the command isn't re-run. For small, one-shot output, skip the file and just read stdout.
- **Verify before you trust.** Validate important findings with a second, independent agent rather than accepting the first pass.
- **Hand off cleanly.** When you pause or hit a boundary, capture remaining work as a plan another agent can pick up (e.g. via `/draft-plan`).

Report back with the synthesis and the decisions — not a transcript of what every agent did.
