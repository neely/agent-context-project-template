# Agent Protocol

## Authority (read if present)
- If a field-level grounding spec applies to this project (GROUNDING.md), it outranks this file.
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
