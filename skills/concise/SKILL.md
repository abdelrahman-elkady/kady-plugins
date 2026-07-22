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

- Simple everyday words. Short sentences.
- No preamble, no recap of what the reader already knows, no self-congratulation, no hedging.
- No decoration: if it fits in a sentence or two, no headers or bullets.
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

**Reporting work** — outcomes and load-bearing facts, nothing else:

Before:
> Replaced the example in skills/concise/SKILL.md. The "before" is now a typical agent completion report — a `## Summary` header, a numbered file-by-file "Changes Made" walkthrough with bolded paths, a "Testing" section, and a "Next Steps" offer — the dry, structured over-reporting agents actually produce. It also now mirrors the exact drifts the rules block: headers as decoration, restating the request, and the closing "Let me know if…".
>
> The "after" is two sentences keeping every load-bearing fact: the new behavior (3 retries, 1s/2s/4s backoff), the two config knobs by name, and the test result (47 pass, 3 new). File paths got dropped deliberately — they don't change what the reader does next, and the config names point to where things live anyway.

After:
> Replaced the example. The "before" is now a realistic completion report — Summary, file-by-file Changes Made, Testing, Next Steps — the same drifts the rules block. The "after" keeps every load-bearing fact (retry behavior, both config names, test results) and drops the file paths, which the config names already locate.
