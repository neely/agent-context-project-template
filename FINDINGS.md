# <Project> — Findings

What this project has concluded. Topical, not chronological. Resolved only —
no open items; those stay in PLAN.md.

Both sections are edited in place. If a result contradicts an entry, correct
that entry. The history lives in JOURNAL.md.

---

## Established

Discovered truths. Facts you did not choose.

### Tripwires
Expected counts, known splits, sanity bounds. Recompute a derived set against
these before you trust it. Hard-stop on mismatch.
- <derived set> — expected <count / bound>.

### Intentional, not bugs
Things that look wrong but are correct. Do not "fix" these.
- <behavior> — <why it's intended>

### Known permanent limitations
- <limitation> — <why it can't/won't be solved, so nobody re-chases it>

### Dead-ends (do not re-explore)
- Tried <X> → got <Y> → rolled back because <reason>.
- Did NOT <tempting shortcut> because <reason it's wrong>.

### Reference
API quirks, schemas, formulas, the regex you fought with, constants —
whatever a session might need to look up. Organize by topic/feature.

#### <Topic>
<content>

---

## Decisions

Choices made, and why. Mark settled ones `(locked)`.

### <Decision name> (locked)
- What: <the decision>
- Why: <the reasoning — this is the part that stops re-litigation>
- Rejected: <what you considered and didn't do, and why>
