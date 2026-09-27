---
name: cross-repo-handoff
description: Use when coordinating work across two or more sibling git repositories driven by separate agent sessions — one repo needs an API route/field/contract from another, hands off a feature or a spec document, or must keep branches and expectations consistent across the pair. Covers both an offline sibling and a live peer session reachable over SendMessage. Also when setting up such coordination for a new repo pair.
---

# Cross-Repo Handoff

## Overview

Coordinate independent sibling repos by **publishing** needs in your own repo, never **pushing** into the other.

**Two channels, one record.** Git is the system of record. A live peer session — another Claude session driving the sibling repo, reachable via `SendMessage` — is an accelerator over that record, never a substitute for it: a message dies with its session (or with compaction), a committed fragment does not.

A capable agent re-derives this design from scratch — so the value of this skill is NOT teaching the mechanism. It is that **both repos use the _exact same_ conventions**, or they silently fail to interoperate (one writes `handoff/`, the other reads `coordination/`; one commits locally, the other reads `origin/`). Follow the conventions below verbatim on both sides.

## When to use

- You're in one repo but the task depends on a sibling repo (backend/frontend, api/client, service/service).
- You need something from the other side (route, field, event), or are handing off a feature or a spec doc.
- A session is live on the sibling repo and you need to ask it something or tell it something.
- You're setting up coordination for a new repo pair.

**Not for:** a monorepo (use normal imports); or a pair that already shares a live contract artifact (extend that instead).

## The one rule

**Write only your own repo's `handoff/`. Read the other's read-only.** Never edit the sibling checkout — it dirties another session's tree and couples the two histories. You publish; they pull. This holds when the peer is live too: ask them to write it, don't reach in.

## Canonical conventions (both repos MUST match verbatim)

Directory `handoff/` at repo root. One file per need — distinct filenames never merge-conflict:

```
handoff/<FROM>-<TICKET|slug>-<NN>.md
```

`<FROM>` = a fixed 2-letter tag per repo (e.g. `BE`/`FE`). `<TICKET>` = ticket id if one exists, else a short kebab slug of the branch. `<NN>` = 2-digit sequence.

Flat YAML frontmatter + body:

```markdown
---
id: BE-LAB-123-01
from: backend
to: frontend
type: ask | initiative | doc-handoff
status: open | done
branch: feature/LAB-123-...
ticket: LAB-123
created: <YYYY-MM-DD>
---

## <one-line summary>

<the ask / initiative detail / doc pointer>

Refs: <repo-relative paths the reader opens across the sibling checkout>
```

`type`: `ask` (need something from the other side) · `initiative` (feature you're starting — include `branch` + `ticket` so the other side creates a matching branch; the ticket tracker owns status, link don't restate) · `doc-handoff` (spec ready — **point to its path, never inline the doc**).

## Branch-scoped, and commit to publish

- A need lives on the branch that raises it; delete it when satisfied so `main`/`master` stays clean.
- **Commit the fragment.** `git show <ref>:` reads git objects, not the working tree — an uncommitted fragment is invisible to the other side.
- Branch names match across repos (both derive from the ticket), so the reader knows which ref to read.

## Reading the other side

Resolve the sibling from git (worktrees move the root — never hardcode `../`); read the branch without checking it out:

```sh
SIB=$(git -C ../<sibling> rev-parse --show-toplevel)
git -C "$SIB" ls-tree <branch> handoff/            # list open needs on that branch
git -C "$SIB" show <branch>:handoff/<FILE>.md
```

Local-only repos: read the **local** branch ref. Coordinating through a remote: `git -C "$SIB" fetch` first, then read `origin/<branch>`. Pick one per pair and state it in the repo's rules.

Read at task start and before claiming cross-cutting work done.

## Is the peer live?

Run `ListAgents` at task start, alongside the read above. Every session self-names and the listing prints `<topic-slug>-<xx> [ref]` plus its state (`idle`, `waiting`).

- The name is a **topic slug, not a repo name** — it hints at the work, not the checkout. Treat a promising row as a *candidate*: message it and have it confirm its repo and branch before you rely on it as the peer.
- **Names repeat.** Two sessions can carry the same slug; append the row's `[ref]` whenever the bare name isn't unique.
- `waiting` means the session is blocked on its own user — a ping may sit unread for a long time. Weigh that in *Drain, then escalate* below.

No candidate → the peer is offline. Everything below is inapplicable; the git protocol above is the whole story.

## Live channel rules

A live peer changes the latency, not the record. What must be committed:

| Situation | Commit a fragment? | Ping? |
|---|---|---|
| Transient question ("which field name did you land on?") | no | yes |
| `ask` / `initiative` / `doc-handoff` — they must act, or the answer outlives the session | **yes, first** | yes |
| Peer offline | yes | n/a |

The test: **if it changes their code or their branch, it gets a file.**

- **Commit before you ping.** A ping pointing at an uncommitted fragment is a dead link — the peer reads git objects, not your working tree.
- **One-line ping,** so the peer can act without a round trip:

  ```
  [handoff] BE-LAB-123-01 on feature/LAB-123 in <repo>: <one-line ask>.
  Read: git -C <path> show feature/LAB-123:handoff/BE-LAB-123-01.md
  ```

- **Drain, then escalate.** After pinging, do every piece of work that doesn't depend on the answer. Still blocked? Tell the user. Never idle-poll, and never invent the contract to keep moving.

## Closing out

Delete your own fragment on its branch before merge. To signal you delivered what the *other* side asked (you can't edit their file), publish your own `status: done` fragment referencing their `id`; they read it and delete their ask. If they're live, ping them as well — the fragment is still what makes it true.

## One-time setup for a repo pair

Put these conventions where each repo auto-loads instructions (`.claude/rules/`, `CLAUDE.md`, `AGENTS.md`) so every future session in that repo speaks the protocol. Each repo sets up **its own** side — don't write the other repo's setup into it.

## Common mistakes

- **Different dir names or frontmatter fields between the two repos** — silently non-interoperable. Match verbatim.
- **Writing into the sibling repo** — dirties its tree, couples histories. Publish in your own.
- **Forgetting to commit** — the reader sees committed refs, not your working tree.
- **Treating a live message as the record** — it dies with the session. Anything actionable is committed too.
- **Pinging before committing** — the peer resolves a ref that doesn't exist yet.
- **Blocking on a reply** — a busy or ended peer stalls you. Drain, then escalate.
- **Asking a live peer to write your fragment into their repo** — needs publish on their own side, not yours.
- **Assuming a session name identifies a repo** — it names the topic. Confirm repo and branch with the peer first.
- **A single shared log file** — merge conflicts plus cross-repo writes. One file per need.
- **Hardcoded `../path`** — breaks under worktrees. Resolve via `git rev-parse --show-toplevel`.
