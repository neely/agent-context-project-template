# Solo Agent Context Kit

A repo-level context system for solo development on `main`, built to survive a cold start — a fresh chat with zero memory should be able to read these files and pick up exactly where you left off.

> **Note — this reflects the initial build.** This document is the founding
> write-up of the concept. The template repo it describes
> ([neely/agent-context-project-template](https://github.com/neely/agent-context-project-template))
> has since been updated, so the live `AGENTS.md` and the other files there are
> the current source of truth — this doc may not mirror them line-for-line.
> Read it for the *why*; read the repo for the *what it is now*.

Five living files plus git. No branches, no pointer files, no extra ceremony — everything below is stripped to what actually earns its place.

**Using it:** give an agent read/write access to the repo (GitLab, GitHub, or whatever your git host is), have it read this doc — or a project's own `AGENTS.md` — and go. The system is built so the agent both reads the context *and* maintains it: you work through the session, it writes state back into these files before you close. A cold chat tomorrow reads the same files and is instantly oriented.

---

## The one principle

Every file exists to survive a cold start. The test for anything you write is:

> Would a fresh chat, with zero memory of this project, do the right thing from this alone?

That's why *why we decided* and *what we rejected* matter as much as *what we did*. A fresh agent has no memory of the reasoning, so if it isn't written down, the agent will happily re-litigate a settled decision or "fix" something that was intentional.

---

## The five files

| File | Reader | Answers | Sync risk |
|------|--------|---------|-----------|
| `README.md` | A human arriving cold | What is this, where's it live, how do I run it | High — rots silently |
| `AGENTS.md` | The agent | How should I behave, what's off-limits | Low — rules change rarely |
| `PLAN.md` | Agent + mid-build you | What's done, what's active, what's next | Medium |
| `NOTES.md` | Agent + mid-build you | Why is it built this way | Medium |
| `JOURNAL.md` | Future you | How did we get here, what was I unsure about | None — append-only |

**Boundaries, so files don't overlap:**
- README = the project *as it exists now*, for a newcomer.
- PLAN = *where it's going* (plus the status block: where it is right now).
- NOTES = *why it's built this way* (timeless, topical).
- JOURNAL = *how we got here* (temporal, append-only, reflective).

If you feel yourself writing roadmap into the README or marketing copy into PLAN, a boundary slipped.

**Where the status block lives:** on PLAN, right at the top. It's the mutable "you are here" snapshot. Pick one home and never duplicate it — a duplicated current-state field is exactly the thing that drifts.

**Why no `pointer.md`:** A common pattern is a tiny standalone file holding only the single most recent session summary — the idea being that a fresh chat reads just that one small file to orient instantly and cheaply, without ingesting the whole history. It's a reasonable instinct, and it's solving a real problem (cold-start orientation). But the status block already does exactly that job, inline — and a *second* file whose whole purpose is to hold current state is one more thing you have to keep in sync. That duplication is precisely what drifts. Same goal, one fewer file to rot. Skip it.

### Two optional additions

**`reference/` — vendored knowledge.** The five files are for what *you* author. When you need to bring in *external* material — API docs, a scraped spec, source you're porting from, a standards document turned into markdown — it doesn't belong in NOTES (which is your reasoning, and which you want to stay skimmable). Put it in a `reference/` folder instead. The distinction: **NOTES is what you decided; `reference/` is what you brought in.** One caveat that makes or breaks this — `reference/` must hold *distilled* material, not raw dumps. A 400-page spec pasted in whole is worse than a link: it's stale context the agent has to wade through. Distill it down to what matters, then vendor that. AGENTS tells the agent to consult `reference/` targeted, only when the task needs it — same progressive-disclosure logic as the NOTES skim, never an always-read.

**Grounding — the rung above AGENTS.** For some work, especially scientific software, there's a layer of authority higher than any single project's rules: *field-level* invariants that come from community consensus, not from you. A domain "grounding" spec encodes those — the things that must be true for the output to be valid, regardless of what any one user wants. When such a spec applies, it *outranks* AGENTS, because validity beats individual intent. You don't author one per project (a blank grounding stub would be meaningless), so it isn't a template file — it lives once, authoritatively, elsewhere, and projects point at it. AGENTS just needs a one-line hook acknowledging the higher rung and deferring to it. On projects with no applicable grounding, the line is a no-op. Worth noting the mechanism is the same one this kit already uses: a grounding's "hard constraints" and this kit's `(locked)` / "intentional, not a bug" markers both tell a fresh agent *don't relitigate this* — they differ only in where the authority comes from (the field vs. you). Example of a real field grounding spec: [OmicsGrounding/proteomics-grounding](https://github.com/OmicsGrounding/proteomics-grounding).

---

## The session loop

### Init (before any code)

> Read `AGENTS.md`, then the status block at the top of `PLAN.md` and the active phase. Skim `NOTES.md` for anything relevant. Tell me the next step to confirm you're oriented — don't write code yet.

If the last session left an embedded handoff prompt in PLAN (see below), point the agent at that instead.

### During the session

Work normally. Commit as you go — plain messages, straight to `main`, as many commits as makes sense. No squashing, no `ai:`/`docs:` prefixes, no branches. The in-session commits are your safety net; if the agent goes down a bad path you can revert the last one or two.

**Targeted edits only.** The agent must not rewrite a whole file to change a few lines. This is the one hard rule that saves real tokens and cuts merge risk.

### Shutdown (before you close)

> We're wrapping up. Do the shutdown routine, and report each step and its result:
> 1. Update the status block in `PLAN.md` (current state + next action).
> 2. Tick any finished PLAN checkboxes.
> 3. Add any new decisions or dead-ends to `NOTES.md`.
> 4. If README describes anything we changed, update it in this same pass.
> 5. Run the debrief (below) and append it to the top of `JOURNAL.md`.
> 6. Commit **and push** — report the commit hash and confirm the push went through.

So the code commits are the trail *within* the session; the wrap-up is a single bookend docs commit at the end. Ask the agent to report each step rather than accept a blanket "done" — that's what makes a skipped checkbox or an unpushed commit visible instead of silent. Push is the one that bites most often: agents routinely commit locally and never push, so it's called out as its own proven step.

Once the push is confirmed, **`/clear`**. The whole point of writing state back into the files is that the conversation is now disposable — the next session cold-starts from disk, which this kit makes cheap and reliable. Clearing costs nothing and stops tomorrow's one-line question from re-billing today's entire conversation.

---

## The debrief

The debrief is the JOURNAL entry. It's the temporal, reflective stuff that isn't a locked decision (that goes in NOTES) and isn't current state (that's the status block). An append-only journal can't drift, which is exactly why this file is safe to keep.

**Cadence:** Q1 and Q5 every session. All five for big sessions.

1. **What are you least confident about, and what would prove each one right or wrong?**
2. What did you assume without stating it?
3. What's the biggest thing I'm missing here?
4. What could I have done differently to make this session more useful?
5. What would you suggest to improve?

---

## The marker conventions

These are the load-bearing part of the whole system — they're what stop a fresh agent from undoing your work. Use them liberally in NOTES and PLAN.

- **`(locked)`** — a settled decision. The agent must not reopen it without being told to.
- **"don't relitigate" / "already decided"** — same, for bigger architectural calls.
- **"intentional, not a bug"** — negative-space documentation. Tells the agent what *not* to fix. Also: "known limitations," "not a bug."
- **Dead-ends recorded as dead-ends** — "tried X, it gave Y, rolled back"; "did NOT guess the URL by pattern-matching." Stops re-exploration of a path you already ruled out.
- **Embedded handoff prompt** — when a phase is a clean stopping point, write next session's kickoff prompt directly into PLAN. Highest-fidelity cold start there is.

AGENTS.md tells the agent to respect all of these.

---

## Why this is also the cheap way to work

Everything above was designed for *cold-start survival*, but the same moves are
what keep an agent's token cost down — context size is the bill, and this kit is
a discipline for keeping context small. Worth naming so a fresh agent doesn't
"helpfully" undo it:

- **Progressive disclosure is a cost control, not just tidiness.** The read
  order (AGENTS → PLAN status → skim NOTES → JOURNAL *only if needed* →
  `reference/` *targeted*) exists so the agent pulls in only what the task needs.
  Distilling `reference/` instead of dumping raw specs, and keeping AGENTS short,
  are the same lever: fewer always-read tokens on every single message.
- **Targeted edits only** is the headline token-saver — rewriting a whole file
  to change three lines re-emits the whole file. (Already a hard rule above;
  this is *why* it's hard.)
- **The markers earn their cost twice.** `(locked)`, dead-ends, and
  "intentional, not a bug" stop the agent from re-exploring settled ground —
  which is both a correctness win and the broad-scanning cost the markers exist
  to prevent.

The one habit the kit was missing, worth adding to the loop:

- **`/clear` between unrelated tasks.** A session sends its *full* conversation
  with every request; leaving one open all day means a one-line question still
  re-bills the whole history (at the cached rate, but re-billed). When you switch
  to unrelated work, `/clear` and re-orient from the files — which is exactly the
  cold start this kit is built to make cheap and reliable. That's the payoff of
  writing state into the files instead of the chat: clearing costs you nothing,
  because the context lives on disk, not in the conversation.

---

## What to skip (and why)

Branch-per-phase, squash merges, semantic commit prefixes, multi-agent issue/PR choreography, a table of contents, reverse-chronological ordering of NOTES, and a separate `pointer.md`. For solo-on-`main` every one is friction without payoff. Keep the branching/agent stuff in your back pocket as "fun to try someday," not the working setup.

(The one place reverse-chron *does* earn its keep is JOURNAL — it's append-only, and you want the newest debrief on top.)

---
---

# File templates

Drop these in and fill them out (already on [template repo](https://github.com/neely/agent-context-project-template) ). Delete the guidance comments once you're rolling.

---

## `AGENTS.md`

```markdown
# Agent Protocol

## Authority (read if present)
- If a field-level grounding spec applies to this project, it outranks this file.
  Defer to it and cite the relevant constraint when a conflict arises — field
  validity beats project preference. (No-op if the project has none. Example:
  github.com/OmicsGrounding/proteomics-grounding)

## Files (read in this order on a cold start)
1. This file — how to behave.
2. PLAN.md — status block (top) + active phase.
3. NOTES.md — why things are built the way they are; skim for relevance.
4. JOURNAL.md — recent debriefs, only if you need the backstory.
5. reference/ — vendored external material (docs, specs, ported source).
   Consult targeted, only when the task needs it — do not read it wholesale.

README.md is for humans arriving cold — not part of your read path, but keep
it in sync (see below).

## How to work
- **Targeted edits only.** Never rewrite a whole file to change a few lines.
  Edit the precise lines.
- **Commit to main, plainly.** Standard commit messages, straight to main.
  No branches, no squashing, no commit-message prefixes. Commit as you go.
- **Respect the markers.** Do not reopen anything tagged `(locked)` or
  "don't relitigate" unless explicitly told to. Do not "fix" anything tagged
  "intentional, not a bug." Do not re-explore anything recorded as a dead-end.
- **Keep README in sync.** If a change alters anything README describes,
  update README in the same pass. It rots silently; treat that as a bug.

## Start of session
Read the files above. Before writing any code, sanity-check that PLAN's status
block, its checkboxes, and NOTES agree with each other and with the actual repo
— flag anything stale or contradictory. (This catches a botched shutdown from
last session for free.) Then state the next step to confirm you're oriented.

## End of session (shutdown routine)
Do these in order, and reply with each step and its result so nothing is
silently skipped — a prose "done!" hides gaps; an itemized report surfaces them.
1. Update the status block in PLAN.md (current state + next action).
2. Tick finished PLAN checkboxes (say "none" if nothing changed).
3. Add new decisions / dead-ends to NOTES.md (with the right markers).
4. Update README if anything it describes changed (say "no change" if not).
5. Run the debrief and append it to the TOP of JOURNAL.md.
6. Commit AND push. Report the commit hash and confirm the remote accepted the
   push — these are two separate operations and "committed" is not "pushed."

## Debrief
Ask Q1 and Q5 every session; all five for big sessions. This is step 5 of
shutdown, but it doesn't depend on the agent remembering to run it — you can
trigger it directly at any point ("run the debrief"), which is the more reliable
habit. Either way the output gets appended to the top of JOURNAL.md.
1. What are you least confident about, and what would prove each one right or wrong?
2. What did you assume without stating it?
3. What's the biggest thing I'm missing here?
4. What could I have done differently to make this session more useful?
5. What would you suggest to improve?
```

---

## `PLAN.md`

```markdown
# <Project> — Roadmap

Live at **<url>** · Repo: **<repo>**

---

## Status
- **Active:** Phase N — <name>
- **Last updated:** YYYY-MM-DD
- **Next action:** <the single next concrete thing>

<!-- This block is the "you are here" pointer. Overwrite it every shutdown.
     It lives ONLY here — never duplicate current state in another file. -->

---

## Conventions
- Keep README.md in sync with what's actually live.
- Add decisions to NOTES.md when made; mark settled ones `(locked)`.
- Check off phases below as completed.

---

## ✓ Phase 1 — <name>
- <what got done> — <why, in a clause> so a fresh agent knows not to undo it
- ...

## Phase 2 — <name>  ← ACTIVE
- [x] <done item>
- [ ] <open item>
- [ ] <open item>

## Future / if needed
- <thing> — <one line on why it's deferred, not just what it is>

---

<!-- Optional: when a phase is a clean stopping point, write next session's
     kickoff here so the cold start is exact.

## Handoff → next session
Start prompt:
> Read AGENTS.md, PLAN.md status block, and NOTES.md "<section>", then
> implement Phase 3: <name>. Key files: <...>. Watch out for <...>.
-->
```

---

## `NOTES.md`

```markdown
# <Project> — Notes & knowledge base

Topical, not chronological. This is what you don't want to re-explain or
re-derive. Timeless reference + the reasoning behind decisions.

---

## Design decisions (locked)

### <Decision name> (locked)
- What: <the decision>
- Why: <the reasoning — this is the part that stops re-litigation>
- Rejected: <what you considered and didn't do, and why>

---

## Intentional, not bugs
Things that look wrong but are correct. Do not "fix" these.
- <behavior> — <why it's intended>

## Known permanent limitations
- <limitation> — <why it can't/won't be solved, so nobody re-chases it>

---

## Dead-ends (do not re-explore)
- Tried <X> → got <Y> → rolled back because <reason>.
- Did NOT <tempting shortcut> because <reason it's wrong>.

---

## Reference
API quirks, schemas, formulas, the regex you fought with, constants —
whatever a session might need to look up. Organize by topic/feature.

### <Topic>
<content>
```

---

## `JOURNAL.md`

```markdown
# <Project> — Journal

Append-only. Newest entry on top. Never edit past entries — this is history,
not current state. One entry per session: the shutdown debrief.

---

## YYYY-MM-DD — <session in a few words>

**Did:** <one or two lines>

**Least confident about (Q1):**
- <thing> — would be proven right/wrong by <test/observation>.

**Suggested improvement (Q5):** <one line>

<!-- For big sessions, also include: -->
**Unstated assumptions (Q2):** <...>
**Biggest thing being missed (Q3):** <...>
**Could've gone better (Q4):** <...>

---

## YYYY-MM-DD — <previous session>
...
```

---

## `README.md`

```markdown
# <Project>

<One-line description of what this is.>

**Live:** <url>

## What it is
<A paragraph for a human who just landed here. What it does, who it's for.
The project as it exists NOW — not the roadmap, not the rationale.>

## Structure
<Brief file/folder map. Enough to orient a newcomer.>

## Running / developing
<How to run it, how to add to it, deploy notes. The practical stuff.>

<!-- Keep this in sync with reality. It's the file most likely to rot because
     nothing breaks when it's wrong. Update it in the same pass as the change. -->
```
