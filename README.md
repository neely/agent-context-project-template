# Solo Agent Context Kit: a project operating system for LLM-based development

A repo-level context system for solo development on `main`, built to survive a
cold start: a fresh chat with zero memory should be able to read AGENTS.md
and pick up exactly where you left off, made possible by a thorough shutdown protocol.

This template is a project operating system for an LLM comprised of six living
files plus git. Answer some starter questions and then you are ready to roll with
"Read AGENTS.md and let's GO!!!" or "Shutdown" commands.

This system is focused on reproducibility, testing, targeted edits, and using either
frequent small commits or batched atomic commits, along with a quasi Architecture Decision
Record (ADR) system, and shutdown protocol that includes a debrief (to catch mistakes
and spot future opportunities), which taken together means this attempts to keep the LLM honest,
documented, and the use of commits/pushes along with a journal means the history
is easy to understand and revert to, lowering the cost of giving more leeway to the
LLM during development.

**Current version: v5** (2026-09-22). Changelog at the bottom.

---

## The one principle

Every file exists to survive a cold start. The test for anything you write is:

> Would a fresh chat, with zero memory of this project, do the right thing
> from this alone?

That's why *why we decided* and *what we rejected* matter as much as *what we
did*. A fresh agent has no memory of the reasoning, so if it isn't written
down, the agent will happily re-litigate a settled decision or "fix" something
that was intentional.

---

## The six files

| File | Reader | Answers | Sync risk |
|------|--------|---------|-----------|
| `README.md` | A human arriving cold | What is this, where's it live, how do I run it | High (rots silently) |
| `AGENTS.md` | The agent | How should I behave, what's off-limits | Low (rules change rarely) |
| `PLAN.md` | Agent + mid-build you | What's done, what's active, what's next | Medium |
| `FINDINGS.md` | Agent + mid-build you | What we've established, and what we decided | Medium |
| `JOURNAL.md` | Future you | How did we get here, what was I unsure about | None (entries never edited) |
| `CLAUDE.md` | Claude Code | Ensures AGENTS.md is read on cold start, now [deprecated](https://github.com/anthropics/claude-code/tree/main/mods/agents-md) | Low (just a reference) |

**Boundaries, so files don't overlap:**
- README = the project *as it exists now*, for a newcomer.
- PLAN = *where it's going* (plus the status block: where it is right now).
- FINDINGS = *what's settled* (discovered truths under **Established**,
  choices under **Decisions**): a quasi Architecture Decision Record (ADR)
  system.
- JOURNAL = *how we got here* (temporal, reflective).

If you feel yourself or your LLM writing a roadmap into the README or marketing copy into
PLAN, a boundary slipped. Note: sometimes running a drift correction or "check my hygiene
between context docs" can be useful.

**Where the status block lives:** on PLAN, right at the top. It's the mutable
"you are here" snapshot. Pick one home and never duplicate it. A duplicated
current-state field is exactly the thing that drifts.

### Two optional additions

**`reference/` - vendored knowledge.** The six files are for what *you*
author. External material like API docs, a scraped spec, source you're porting
from goes in a `reference/` folder instead. The distinction: **FINDINGS is
what you concluded; `reference/` is what you brought in.** It must hold
*distilled* material, not raw dumps. This knowledgebase has an opportunity to become
more formal and searchable without killing context size, but this is beyond this
simple template (in the current version at least).

**Grounding - the rung above AGENTS.** For some work, especially scientific
software, there's a layer of authority higher than any single project's rules:
field-level invariants that come from community consensus, not from you. When
such a spec applies, it *outranks* AGENTS, because validity beats individual
intent. AGENTS carries a one-line hook deferring to it; on projects with none,
the line is a no-op. Example:
[OmicsGrounding/proteomics-grounding](https://github.com/OmicsGrounding/proteomics-grounding).

---

## Using it

Give an agent read/write access to the repo (GitLab, GitHub, or whatever your
git host is), have it read the project's `AGENTS.md`, and go. The system is
built so the agent both reads the context *and* maintains it: you work through
the session, it writes state back into these files before you close. A cold
chat tomorrow reads the same files and is instantly oriented.

1. **Use this template** to create your repo.
2. Paste the kickoff prompt from `_delete-after-setup/how-to-start.md`.
3. Answer the five first-run questions in `AGENTS.md`. The agent then fills in
   PLAN, swaps in the real README, and deletes its own setup section along
   with `_delete-after-setup/`.

`_delete-after-setup/` holds everything that shouldn't survive into your
project: the README stub and the kickoff prompt.

---

## The session loop

**Init** - read `AGENTS.md`, then PLAN's status block and active phase. Skim
FINDINGS. State the next step before writing code.

**During** - work normally. Commit as you go, plain messages, straight to
`main`. Targeted edits only: never rewrite a whole file to change a few lines.

**Shutdown** - run the routine in AGENTS.md and have the agent report each
step and its result. A prose "done!" hides gaps; an itemized report surfaces
them.

---

## The marker conventions

This is the crux of the project operating system: they're what stop a
fresh agent from undoing your work.

- **`(locked)`** - a settled decision. Do not reopen without being told to.
- **"intentional, not a bug"** - negative-space documentation. Tells the agent
  what *not* to fix.
- **Dead-ends recorded as dead-ends** - stops re-exploration of a path you
  already ruled out.
- **Embedded handoff prompt** - when a phase is a clean stopping point, write
  next session's kickoff prompt directly into PLAN. Highest-fidelity cold
  start there is.

---

## What to skip (and why)

Branch-per-phase, squash merges, semantic commit prefixes, multi-agent
issue/PR choreography, and a separate `pointer.md`. For solo-on-`main` every
branch is friction without payoff. Still, using in small groups adopting a git-flow
organization or similar would be smart.

---

## Changelog

**v5 (2026-09-22)**
- AGENTS.md now tells the agent not to widen scope silently. State the
  intended outcome and the files likely to change before editing; stop and
  report if the work grows past that.
- Added a line to the prime directive: external or agent-managed memory is a
  hint, not project authority. Verify a recalled claim against tracked files,
  git history, or a command before using it.
- Shutdown step 6 is new: re-check the result from current files and observed
  output before committing. An earlier description of the work is not
  evidence on its own.
- The changelog version headers and one leftover table cell no longer use an
  em-dash, to match house style.
- Fixed the `_delete-after-setup/` line, which claimed it held a changelog
  source file. It never did; git history shows only the README stub and the
  kickoff prompt were ever added there.
- Cleaned up a stray `= quasi Architecture Decision Record (ADR) system`
  fragment on the FINDINGS boundary line into a full sentence.

**v4 (2026-08-23 / 08-29)**
- The prime directive now says which files count. "A file on disk" let an
  agent-private memory store through, because such a store is a file on disk.
  A store an agent wrote for itself is not a source of fact.
- A ticked PLAN checkbox now carries an evidence standard. Tests that pass are
  not proof on their own.
- Fixed four drift items. README said five files in the heading and six in
  the text; it is six, with CLAUDE.md. The v4 date did not agree across the
  AGENTS.md stamp, the README, and the changelog.
- First-run step 3 now also removes the Tripwires block from FINDINGS.md when
  the project is not data-driven. The block had no rule left to serve.
- First-run step 5 now records how to find the journal issues when the journal
  moves to GitHub. Before, the journal left the read path with no pointer.
- NOTES.md becomes FINDINGS.md, split into **Established** (discovered truths)
  and **Decisions** (choices, and why). Both edited in place. The rename is the
  point, not the split. Agents read "notes" literally and put anything in it.
  The file was never for notes. It holds what the project has concluded. You can
  think of FINDINGS.md like ADR-lite, but it was being treated more like a 
  journal simply based on the file name.
- Tripwires get an explicit heading. AGENTS.md pointed at a section of NOTES
  that did not exist.
- JOURNAL.md keeps the 5 most recent entries. Older entries move unchanged to
  journal/YYYY-MM.md, so the file stays cheap to rewrite.
- README is now the kit's own README. The project stub moved to
  `_delete-after-setup/`, along with the kickoff prompt.
- AGENTS.md carries a version stamp, so any project shows which generation of
  the kit it was born from.
- First-run adds a fifth question: where the journal lives.
- Added CLAUDE.md in the repository root that points to AGENTS.md (`@AGENTS.md`)
  to ensure Claude Code reads AGENTS.md on cold start even if it does not
  automatically pick up AGENTS.md.

**v3 (2026-08-17 / 08-19)**
- Commit messages, and everything the agent writes in PLAN, NOTES, and
  JOURNAL, follow ASD-STE100.
- NOTES.md is resolved-only. Open items live in PLAN.md, never NOTES.
- Finished PLAN phases collapse to one line under Completed instead of keeping
  a full writeup around forever.
- PLAN.md opens with a Purpose and Non-goals line now, asked for on first run.
- Added a fourth line to the prime directive: a contradicting result outranks
  your hypothesis.
- LICENSE.md carries NIST terms, with a first-run question to remove it when
  it does not apply.

**v2 (2026-07-27 / 08-05)**
- Added the prime directive, reproducibility pinning, and tripwire sections,
  straight out of what RM 8048 taught me.
- Added the self-consuming first-run section. Asks its setup questions once,
  then deletes itself.
- Generalized the prime directive to every project, not just data-heavy ones.
- Removed the duplicate copy of the guide that had been living in the repo.

**v1 (2026-07-13)**
- Original kit.

---

Write-ups: [the original post](https://neely.github.io/agent-context-kit/) and
[what changed since July](https://neely.github.io/solo-agent-context-kit-update/).
