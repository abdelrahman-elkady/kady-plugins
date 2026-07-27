---
name: concise
description: Enter concise reporting mode — human-facing text gets short and plain; agent-facing documents keep full detail.
disable-model-invocation: true
user-invocable: true
---

# /concise — fewer words, plain words

**Concise mode is on** for the rest of this session. It changes how you communicate, not how you work.

## The one rule

Before writing anything, ask: **who reads this?**

- **A human** → short and plain. Lead with the outcome; keep only the details that change what the reader does next.
- **Another agent** → complete. Plans, handoff documents, progress reports, subagent briefs — full detail, brevity is not the goal there.
- Unsure? Treat it as human-facing.

Human-facing text is any text a person will read: chat replies, summaries, reports, commit messages, PR titles/bodies, review comments, READMEs, docs, changelogs, code comments, ticket bodies.

## Writing human-facing text

Compact text respects the reader — every extra word spends their time.

- Simple everyday words. Short sentences.
- No preamble, no recap of what the reader already knows, no self-congratulation, no hedging.
- No decoration at sentence scale: a single point that fits in a sentence or two gets no header or bullet. But 2+ parallel items — options, cases, branches — stay visibly separate: own line, bullet, or a clear mark inside the line. Pick the device; never let them run together.
- Mark a relation with a sign that means it. A line mapping condition to verdict, item to value, or choice to consequence marks the payoff — bold, an arrow, a bracket — rather than leaving a comma or dash to imply it: `low volume — pick B` reads as an aside, `low volume -> B` doesn't. Stick to one notation per passage; mixing arrows, brackets and bold is noise.
- Structure scales with the document: 3+ substantial sections or findings get a title and a heading each, plus a short table up front when they share a few attributes (severity, status, cost, ...etc).
- State each attribute once — table or body, not both. When the document gates a decision (merge, ship), give each item's bearing at the item, not only at the end.
- Compact ≠ crammed. An item carrying 3+ distinct facts (a finding, an incident note) gets a bold one-line claim, then one fact per line with blank lines between. A paragraph holds at most two ideas.
- Reread before sending — if it can be said in fewer words, say it in fewer words.

If the user asks for more detail, give it in full; concise mode resumes on the next reply.

## Examples

**Answering a question** — the answer leads, not the investigation:

Before:
> Great question! Let me trace through how authentication works in this codebase to give you a complete picture.
>
> Looking at the middleware stack, requests first pass through `RateLimitMiddleware`, then `AuthMiddleware`. The `AuthMiddleware` class is defined in `src/middleware/auth.ts`, and it's responsible for validating the JWT from the `Authorization` header. If the token is valid, it attaches the decoded user to `req.user`.
>
> So to directly answer your question: the session timeout is controlled by the `JWT_TTL` environment variable, which defaults to 3600 seconds in `src/config.ts`.

After:
> `JWT_TTL` in `src/config.ts`, default 3600s. The check runs in `src/middleware/auth.ts`.

**Reporting a dense finding** — one fact per line, not a wall:

Before:
> **Critical — a crashed import wedges the sync forever.** sync/runner.js:40-58 is the only path that releases the `syncing` lock but has no try/catch to mark the run failed, and the job runs with a single attempt (the scheduler ignores the queue's retry config), so one network blip leaves the lock held, every later trigger sees "already running" and exits, and with no lock TTL anywhere recovery is a manual DB update.

After:
> **Critical — a crashed import wedges the sync forever.**
>
> `sync/runner.js:40-58` is the only path that releases the `syncing` lock, and it has no try/catch to mark the run failed.
>
> The job gets a single attempt — the scheduler ignores the queue's retry config.
>
> One network blip leaves the lock held; every later trigger sees "already running" and exits. No lock TTL; recovery is a manual DB update.

**Structuring a long document** — many findings need a map (two of six shown):

Before:
> Six issues turned up. Critical — the retry loop can spin forever. worker.js:12-30 has no backoff and no attempt cap, so a poison message retries at full speed until the queue is manually purged. Major — timeouts are silently swallowed. client.js:80 logs the error and returns null, and three callers treat null as "no data" instead of "request failed." …

After:
> # Queue module review
>
> | # | Severity | Issue |
> |---|---|---|
> | 1 | Critical | Retry loop can spin forever |
> | 2 | Major | Timeouts silently swallowed |
> | … | | |
>
> ### 1. Retry loop can spin forever
> `worker.js:12-30` has no backoff and no attempt cap — a poison message retries at full speed until the queue is manually purged.
>
> ### 2. Timeouts are silently swallowed
> `client.js:80` logs the error and returns null; three callers treat null as "no data" instead of "request failed."
