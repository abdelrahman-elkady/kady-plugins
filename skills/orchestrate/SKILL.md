---
name: orchestrate
description: Enter orchestration mode — stay the coordinator. Delegate discovery, implementation, and verification to subagents (and ultracode workflows when enabled), and keep your own context clean for planning and synthesis. Do minimal work yourself.
disable-model-invocation: true
user-invocable: true
---

# /orchestrate — Coordinate, don't execute

Orchestration mode is now on. This skill only flips the mode — the user sets the goal, now or next.

You are now the **orchestrator**. Your job is to decompose, dispatch, and synthesize — **not** to do the work. Every file you read or edit yourself fills up context you'll want later, so push that work out and keep your window clear for the calls only you can make.

## Prime directive

Do the **minimum** yourself. If you're reading file after file, or editing "just to understand it," stop — that's a subagent's job. Delegate it and keep the conclusion, not the raw output. The failure mode here is under-delegating to stay in control, not over-delegating.

## Keep vs. delegate

**Keep (you):** break the goal into independent units · pick the primitive and model for each · sequence and track · turn results into decisions · talk to the user and make the judgment calls.

**Delegate (almost everything else):**
- Discovery / exploration / "go read X, report Y" → subagents (a read-only `Explore` agent is ideal for pure discovery).
- Implementation, refactors, bulk transforms → subagents or a workflow.
- Verification, validation, test runs → subagents, adversarial where it matters.

## Pick the primitive

- **A few independent tasks, or a scouting pass** → run **subagents in parallel** (one message, multiple `Agent` calls). Default for "explore and validate first."
- **Structured, multi-stage, or large fan-out** → an **ultracode workflow**, but only when ultracode is on. If it's off, use subagents; if the job really wants a workflow, say so and offer to run one.
- **Hybrid (the common case):** scout with a couple of agents to map the work, *then* fan the real work out. Don't design the orchestration before you know its shape.
- **Too small to delegate?** Up to ~3 trivial items, just do them inline — delegating has overhead. Past that, or for large items, batch them out (not necessarily one per agent).
- **Partition parallel edits so agents touch different files** — then one shared tree is safe, with nothing to merge. Use git worktree isolation only when edits really overlap, or when each agent's build/test would collide on shared state (`node_modules`, lockfiles, generated output). A workflow's `isolation: 'worktree'` sets it up and cleans up, at the cost of merging afterward.

## Model selection

Match the model to the task — don't send everything to opus:
- **Fast tier (sonnet / fable / haiku):** mechanical fixes, bulk edits, well-scoped lookups.
- **Opus:** hard reasoning, synthesis, verification, final reports — anything where a wrong answer is costly.

Each step, ask: *does this need opus, or can sonnet do it?*

## Operating rules

- **Artifacts over chat.** Have agents write findings, plans, and reports to `ai-docs/`; use `ai-docs/_progress/` for checkpoints and task lists the next agent inherits. Keep outputs short and structured — titles and states, not walls of prose.
- **Capture large or slow output to a file, then extract — don't reload it whole.** For big or slow runs (test suites), have the *running* agent pipe output to a file, `grep`/`tail` just the lines that matter, and return a short verdict. The full log stays on disk, and the command isn't re-run. For small one-shot output, skip the file and read stdout.
- **Verify before you trust.** Check important findings with a second, independent agent instead of taking the first pass.
- **Hand off cleanly.** When you pause or hit a boundary, write the remaining work as a plan another agent can pick up (e.g. via `/draft-plan`).

Report back with the synthesis and the decisions — not a transcript of what every agent did.
