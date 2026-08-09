---
name: describe-pr
description: Write or rewrite a pull request description and title — concise and high-signal, tickets named up top, operational notes surfaced, a how-to-review block handing the reviewer a reading order, a mermaid diagram only when it shows what prose can't. Use whenever a PR body is being drafted or edited, including `gh pr create` / `gh pr edit`, "open a PR", "write the PR description", "update the PR body". Not for reviewing someone else's PR.
user-invocable: true
---

# /describe-pr — say only what the diff can't

Two people read a PR body: a reviewer deciding where to look, and whoever deploys it deciding what to do by hand. Write for those two and nobody else. **The diff already lists the files, the functions and the tests; the tickets already hold the backstory. Your only job is what neither of them shows.**

**Length tracks decisions, not files**: the more mechanical the diff, the *shorter* the body, and prose walking a reader through how the change works is padding however true it is. The reading path is navigation — it sits outside this, under rules of its own.

Read the real diff (`gh pr diff`, or `git diff <base>...HEAD`) and fetch every ticket you cite before writing a word. Never invent a ticket key, never claim tests pass unless you ran them, never describe work that isn't in the diff.

## Load concise first

Read its `SKILL.md` before drafting — `.claude/skills/concise/`, `~/.claude/skills/concise/`, or `~/.claude/plugins/**/skills/concise/`. Not via the Skill tool: concise sets `disable-model-invocation`, so the call is rejected. **It governs the register and the separation; this file governs what goes in and how tight it gets.** Its *give each distinct idea its own visible spot* binds here in full: a PR body keeps concise's `##` headings and drops its `###` example, but the separation lands unchanged, in bold lead-ins and bullets.

Not installed: write the PR anyway — a hint, never a gate — and say so once in chat, never in the body: `npx skills add abdelrahman-elkady/kady-plugins --skill concise -a claude-code`.

## The diagram test

Add a mermaid diagram when it makes a relationship visible that prose can only list — several distinct paths converging on one outcome, a race, a state machine, an ordering that has to hold, a wiring that differs before and after. One picture of the shape beats three paragraphs walking a reader through it, and it is the one place the holistic view belongs.

**Settle it on the diff, before a line of prose exists**: name in one sentence the non-obvious thing a picture would carry. Named, draw it and never write that prose at all — **the diagram buys a paragraph back, it does not cost one**. Unnamed, there is no diagram.

- **Earns it:** *every route out has to exit non-zero* — arrows from the handler, from the flush timeout, and from a signal that races the flush, all landing on one exit node.
- **Earns it:** *the retry moved from the caller into the worker* — before-and-after wiring where the boxes are the same and only the arrows moved.
- **Decoration, omit:** a box per bullet · an unchanged, unbranching A→B→C a sentence already states · a redrawn file list.

## What earns a block

Default is nothing. **A highlight is something a reviewer would still get wrong after reading the whole diff — not something they would reach slower.** Run it on every fact separately — never on the decision it hangs off. *True* and *worth knowing* are neither.

**A block is a bold lead-in, what changed, and at most one sentence of evidence.** Nothing else — not the mechanism, not the alternative you rejected, not what you checked and found clean. **Evidence is its own short sentence, never a clause chained onto the first**: one needing a semicolon or a second em dash is two. A block that wants a second idea is two blocks, however closely the two ride together.

A list holds items of **one kind** — a single decision's members, or separate changes under one heading — one line each, in the same shape. A lone item stays a sentence; a sub-bullet carries a value of the line above it.

```markdown
[ABC-1234](https://tracker.example.com/browse/ABC-1234) caps importer retries and sends failed rows to a dead-letter queue.

## Changes

- An unparseable row used to retry until someone purged the queue by hand. It stops after three attempts now. One row logged 40k attempts in a day.
- Failed rows carry the batch id and the parse error.
- Retry limits are per batch, not per worker:
  - `IMPORT_MAX_RETRIES` — no default
  - `IMPORT_DLQ_TOPIC` — defaults to `rows.dead`

## Before merging

Set `IMPORT_MAX_RETRIES` in staging and prod.

## Not covered

- **The ticket's third condition** is unverified — it needs a batch large enough to page.
- [ABC-1240](https://tracker.example.com/browse/ABC-1240): the reconciler still reads the old retry column.
```

**Tickets first, unmistakable.** Key, full link, what each one *is*, the relationship when there is one — *same defect, filed twice* — and any decision a ticket left open with which way you went.

**One line of why.** What the old code did wrong, in the present tense of the old code.

**Behavior, not files.** What the system does differently now — the outcome, not the mechanism that produces it. In prose, name a file only when the name is the news: a rename, a new module, a moved boundary.

**What a human must do that merging won't do for them.** Pre- and post-merge steps, new env vars, migrations, feature flags, config and infra changes, breaking changes. None of it is inferable from the diff, so **the step never has to earn its place**: write the action and who takes it, in the imperative, with no argument for it. Confirming that nothing needs doing is not a step.

**What the PR leaves undone.** A ticket only partly closed says so. Work you didn't do stays named as not done, one line each.

Anything that doesn't apply is omitted, heading and all: no `## Testing` reading "tests added", no empty screenshots section, no "N/A". Silence means none.

**A `##` separates kinds of material, not topics inside one** — what shipped, against what is still owed. It never opens a section to fill.

**Nothing below `##`.** A `###` is how three items become three sections that each fill themselves; inside one, that job is a bold lead-in's or a bullet's. Never a sentence that announces a count: *Two things to note* is a heading with extra words that commits the block to covering everything under it.

**Out entirely:** a file-by-file change list · a test-by-test breakdown or coverage note · line counts · restating the ticket · the mechanism behind a change · the alternative you rejected · what you checked and found clean · a value merging sets by itself · a summary of the summary · anything a reviewer reads faster in the diff itself. **Evidence is the exception**: a log line, a query result, a count from the data — whatever lets a reviewer check a claim instead of trusting it stays, in one sentence. A fold launders none of the rest — what's cut is cut, collapsed or not.

## The reading path

GitHub sorts the diff alphabetically, which is never the order that makes a change comprehensible. Close the body with the order that is — last, below the diagram and the open items, folded behind a heading, not the grey triangle a reviewer scrolls past.

```markdown
<details>
<summary><h2>🧭 How to review</h2></summary>

➞ **The write path** — read in order

`routes/thing.js` `create()` → `services/thing.js` `enqueue()` → the `things` insert

Every branch has to leave the row and the job agreeing.

The duplicate-key early return is the one that doesn't — it commits the row and skips the enqueue.

➞ **Who else enqueues** — both bypass the route above

- `jobs/backfill.js` — same contract, no retry; a failed enqueue is silent here.
- `cli/import.js` — same, and it runs unsupervised.

</details>
```

Write one only if you can name the file a reviewer should open **second**. When that's "any of them" — one file changed, or six independent leaves — there's no order to teach and no block.

**Two paths, three at the outside**, load-bearing first, then a different angle: the failure route, the other entry points, the change walked back from its consumer.

**One idea per paragraph, a blank line between every one** — the `➞` holds a path together once the blank lines have pulled it apart: everything from one marker to the next is one path.

- **Label** — `➞`, then a bold name, alone on its line. A trailing `— gloss` earns its place only by saying what shape follows: `— read in order`, `— all three point at config/crash-policy.js`.
- **Route** — files and symbols in the order they run, `→` between hops, nothing else: no clause hanging off a hop, no aside in parentheses, never line numbers, which rot on the next push. It *skips* files, and that is what makes it a route and not the change list.
- **Reason** — one paragraph per thing worth knowing, not one per path. The invariant the walk proves is one; the guard that makes it hold at the edge is another.
- **Fan-in** — sites reaching the same target for *different* reasons get a bullet each: the path, an em dash, its own reason. One reason covering all of them stays a comma list on the route line.

A spec earns a hop only at the end of a route, where it pins the invariant faster than the code states it.

**Nothing load-bearing hides behind a fold.** An operational step, an open item, a question you want answered — those stay visible, or go in an inline comment where a reviewer can reply. **Out of the block:** a checklist to tick · "look carefully at X" with no route.

Mechanics: the summary is always `<summary><h2>🧭 How to review</h2></summary>`, verbatim — HTML because `##` inside `<summary>` renders as literal `##`, and GitHub styles `summary h2` inline-block so it sits beside the triangle rather than below it · blank line after `</summary>`, or GitHub renders the inside raw · `➞` is the literal character, not an entity · every break is a blank line, never `<br>` — GitHub doubles it into `<br><br>` in a PR body.

## The title

One line, no padding: the repo's commit convention (often `type(scope): summary`), the ticket key where the repo puts it, and the change stated as behavior — `fix(ABC-1234): exit non-zero on uncaught exception`.
