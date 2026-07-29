---
name: describe-pr
description: Write or rewrite a pull request description and title — concise and high-signal, tickets named up top, operational notes surfaced, a how-to-review block handing the reviewer a reading order, a mermaid diagram only when it shows what prose can't. Use whenever a PR body is being drafted or edited, including `gh pr create` / `gh pr edit`, "open a PR", "write the PR description", "update the PR body". Not for reviewing someone else's PR.
user-invocable: true
---

# /describe-pr — say only what the diff can't

Two people read a PR body: a reviewer deciding where to look, and whoever deploys it deciding what to do by hand. Write for those two and nobody else. **The diff already lists the files, the functions and the tests; the tickets already hold the backstory. Your only job is what neither of them shows.** Roughly a line of prose per file changed is the right order of magnitude — past ~20 lines you are padding. The reading path is navigation, not prose — it sits outside that budget, under a cap of its own.

## Facts come from the diff and the tickets

Read the real diff (`gh pr diff`, or `git diff <base>...HEAD`) and fetch every ticket you cite before writing a word. Never invent a ticket key, never claim tests pass unless you ran them, never describe work that isn't in the diff.

## What earns a line

**Tickets first, unmistakable.** Key, full link, and what each one *is*. Say the relationship out loud when there is one — two tickets on the same defect, or a ticket that left a decision open and which way you went.

> Fixes **[ABC-1234](https://tracker.example.com/browse/ABC-1234)** (Task) and **[ABC-1240](https://tracker.example.com/browse/ABC-1240)** (Bug) — same defect, filed twice.

**One line of why.** What the old code did wrong, in the present tense of the old code.

**Behavior, not files.** What the system does differently now — one line per real decision, naming the calls a reviewer would otherwise question. In prose, name a file only when the name is the news: a rename, a new module, a moved boundary.

**What a human must do that merging won't do for them.** Pre- and post-merge steps, new env vars, migrations, feature flags, config and infra changes, breaking changes. These are the highest-value lines in the body — a reviewer who misses one breaks prod — and none of them are inferable from the diff, so none of them get cut.

**What the PR leaves undone.** A ticket only partly closed says so, in a closing line. Work you didn't do stays named as not done.

Anything that doesn't apply is omitted, heading and all: no `## Testing` reading "tests added", no empty screenshots section, no "N/A". Silence means none.

**Out entirely:** a file-by-file change list · a test-by-test breakdown · line counts · restating the ticket · a summary of the summary · anything a reviewer reads faster in the diff itself. A fold launders none of these — what's cut is cut, collapsed or not.

## The diagram test

Add a mermaid diagram **only if it makes a relationship visible that the prose does not** — several distinct paths converging on one outcome, a race, a state machine, an ordering that has to hold. Then delete the prose it supersedes: a diagram substitutes, it never accumulates.

- **Earns it:** *every route out has to exit non-zero* — arrows from the handler, from the flush timeout, and from a signal that races the flush, all landing on one exit node. The reader sees the invariant; prose can only assert it.
- **Decoration, omit:** a box per bullet · a linear A→B→C a sentence already states · one caller and one callee · a redrawn file list.

Can't name in one sentence the non-obvious thing it carries? Then there's no diagram to draw. Default to none.

## The reading path

GitHub sorts the diff alphabetically, which is never the order that makes a change comprehensible. Close the body with the order that is — last, below the diagram and the open items, under its own heading with the paths folded beneath. The heading carries the visibility: a bare grey triangle at the end of a long body is exactly what a reviewer scrolls past.

```markdown
## 🧭 How to review

<details>
<summary>The flow, then everyone else calling enqueue()</summary>

**The flow** — `routes/thing.js` `create()` → `services/thing.js` `enqueue()` → the `things` insert
Every branch has to leave the row and the job agreeing; the duplicate-key early return is the one that doesn't.

**From the other end** — `jobs/backfill.js`, `cli/import.js` → `enqueue()`
Same contract, and neither retries.

</details>
```

Write one only if you can name the file a reviewer should open **second**. When that's "any of them" — one file changed, or six independent leaves — there's no order to teach and no block.

**Two paths, three at the outside**, load-bearing first, then a different angle: the failure route, the other entry points, the change walked back from its consumer. A third earns its slot only by answering something the first two don't.

**A path is two lines, never one.** The route is scanned, the reason is read — fuse them and the reviewer has to parse a sentence to recover an order.

- **Route** — a bold label, then files and symbols in the order they run, `→` between hops. Nothing else on that line: no clause hangs off a hop. A fan-in is a comma list and is still a route.
- **Reason** — the next line down, one or two sentences: what the walk buys the reader, and the one thing along it that can be wrong. It may re-name a symbol from the route; it introduces no new file.

A path *skips* files, and that is what makes it a path and not the change list. Never line numbers; they rot on the next push. A spec earns a hop only at the end of a route, where it pins the invariant faster than the code states it.

**Nothing load-bearing hides behind a fold.** An operational step, an open item, a question you want answered — those stay visible, or go in an inline comment where a reviewer can reply.

Mechanics: the heading is always `## 🧭 How to review` — at `##`, because GitHub rules an `h2` with a hairline and that rule is half the anchor · blank line after `</summary>`, or GitHub renders the inside raw · reason goes on the bare next line, no blank line and **no `<br>`** — GitHub already breaks a single newline in a PR body, and adding the tag renders `<br><br>`, re-opening the paragraph gap that would unpair the reason from its route · blank line *between* paths · the summary is plain text naming the angles, since the heading already said what the block is — never "Details", never "click to expand".

**Out of the block:** a clause per changed file · "look carefully at X" with no route · a checklist to tick · a reason smuggled back onto the route line.

## The title

One line, no padding: the repo's commit convention (often `type(scope): summary`), the ticket key where the repo puts it, and the change stated as behavior — `fix(ABC-1234): exit non-zero on uncaught exception`.
