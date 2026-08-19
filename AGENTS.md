# Agent Protocol

## Authority (read if present)
- If a field-level grounding spec applies to this project (GROUNDING.md), it outranks this file.
  Defer to it and cite the relevant constraint when a conflict arises — field
  validity beats project preference. (No-op if the project has none. Example:
  github.com/OmicsGrounding/proteomics-grounding)

## The prime directive: never assume, always check
This project's entire credibility rests on this. It is not a style preference.
It applies to every project, data-driven or not.
- **Every fact you state — about code, config, state, or (in data projects) a
  measurement, identifier, count, set membership, or derived value — comes
  from a FILE on disk or a COMMAND/SCRIPT that reads one. Never from memory or
  recollection.** "I think this function does X" or "I think this entity
  belongs to that category" is forbidden; read the file, run the check, and
  emit what you actually saw.
- If a fact isn't in something you can read, either REQUEST it or WRITE a
  script/query/command to produce it. Do not fill the gap from training
  knowledge.
- **For any EXTERNAL or citation claim** (a paper's figures, a DOI, a current
  name in a controlled vocabulary, an API's behavior, a library version, a
  tool's limit): surface it for verification — quote exactly what the source
  currently asserts and flag it for check. Do not silently trust or silently
  "correct" an external claim from memory.
- **A contradicting result outranks your hypothesis.** If a test, run, or
  measurement disagrees with what you expected, report the disagreement and
  stop. Do not re-interpret it, adjust assumptions, or argue until it agrees.
  "Expected behavior" is not a valid conclusion when the result contradicts
  the goal.

## Reproducibility is locked (data-driven projects; skip if not applicable)
- **External data dependencies are pinned to a specific snapshot or version.**
  State this in any Methods text. A reader re-running against current upstream
  gets drift — that's expected.
- **Verify-regenerate is free and encouraged:** re-run scripts to confirm they
  reproduce the pinned outputs. Do this after any migration or edit that could
  touch a derived number.
- **Change-regenerate (re-running against CURRENT upstream and adopting the new
  data) happens ONLY on explicit user request** — and MUST be preceded by an
  enumerated downstream-impact trace: which caches, counts, figures, and docs
  would change. Never change-regenerate incidentally while doing other work.
- **Manual curation and pinned values change ONLY by deliberate, recorded
  edits.** "Re-derive from raw" faithfully reproduces AUTOMATED steps only;
  anything manual lives in data+code or it silently reverts.

## Tripwire every derived set (data-driven projects; skip if not applicable)
- Recompute against a known count before trusting or interpreting any derived
  set. **Hard-stop on mismatch.** Numbers are certified against the pipeline's
  own scripts, not eyeballed.
- When an aggregation looks surprising, INSPECT THE RAW PRE-AGGREGATION
  DISTRIBUTION before trusting it.
- Project-specific tripwires (expected counts, known splits, sanity bounds) are
  defined in NOTES.md.

## Files (read in this order on a cold start)
1. This file — how to behave.
2. PLAN.md — status block (top) + active phase.
3. NOTES.md — why things are built the way they are; skim for relevance.
4. JOURNAL.md — recent debriefs, only if you need the backstory.
5. reference/ — vendored external material (docs, specs, ported source).
   Consult targeted, only when the task needs it — do not read it wholesale.

README.md is for humans arriving cold — not part of your read path, but keep
it in sync (see below).

## First-run setup (delete this section once completed)
The presence of this section IS the signal that first-run setup hasn't been
done for this repo — that's how a future session knows whether to run this
step. Do it before making any commits. Ask all four questions below in one turn.

**Q1 — Commit style** (two choices):
- **A — Frequent small commits.** Commit each meaningful change as its own
  commit, as it happens. Simple, real-time visibility into progress as work
  happens.
- **B — Batched atomic commits.** Group logically-related file changes (a
  code change + the doc update explaining it) into a single commit. Cleaner
  history, cleaner reverts — one commit reads as one coherent decision —
  but requires holding related edits together before committing rather than
  committing each as it's finished.

**Q2 — Is this a data-driven project?** Does it derive facts, measurements,
identifiers, counts, or figures from files/scripts — such that reproducibility
pinning and derived-set tripwires apply? (Yes/No.)

**Q3 — Purpose and non-goals.** What is this project for, in one concrete
sentence? Anything it deliberately won't do?

**Q4 — NIST / U.S. government license?** Is this a U.S. government work that
should carry the NIST license in LICENSE.md? (Yes/No.)

Once answered:
1. Update the "Commit to main, plainly" line under **How to work** below to
   state the chosen commit style explicitly.
2. Fill in PLAN.md's Purpose and Non-goals lines from the Q3 answer.
3. If Q2 was **No**, delete the two sections tagged
   "(data-driven projects; skip if not applicable)" — "Reproducibility is
   locked" and "Tripwire every derived set" — in full. If **Yes**, leave them
   and drop the "; skip if not applicable" caveat from both headings.
4. If Q4 was **No**, delete LICENSE.md. If **Yes**, keep it.
5. Delete this entire "First-run setup" section.

Make all edits in the same commit.

## How to work
- **Targeted edits only.** Never rewrite a whole file to change a few lines.
  Edit the precise lines.
- **Commit to main, plainly.** Standard commit messages, straight to main.
  No branches, no squashing, no commit-message prefixes. Commit as you go.
- **Write in ASD-STE100.** Simplified technical English — short sentences,
  one idea each. Applies to commit messages and everything you write in
  PLAN, NOTES, and JOURNAL.
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
