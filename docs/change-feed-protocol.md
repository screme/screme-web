# The Change Feed — protocol

**What it is.** One shared, append-only log in Notion, under *SystemOne Memory & Context
Repository*, answering the single question no surface in this workspace could answer before:
**"what moved since I last looked?"**

Sort by `When` descending, read until you recognize something. That's the answer.

**Specified by Todd, 2026-09-15.** This file documents the mechanism and lives in Claude Code's
lane. The append-only *rule* is his; see "Standing amendment" below.

---

## Why it exists

Before it, that question had exactly one answer path: ask an agent to go and look. Every time it
came up on 2026-09-15 — what did ScrumMaster change, is the site still correct, did the deploy
run, what did Todd decide — the answer came from a session re-deriving it live. That is a tax
paid per question, forever, by whoever happens to be asking.

A log pays it once, at write time, by the party who already knows.

## The rules

**Append only.** You may add rows. **You may never edit or delete another party's row.** A row is
what that party observed at that moment; correcting it later destroys the only thing a log is for.
To correct the record, **append a new row** that supersedes it — `Action: Superseded` — and say
what it replaces. The wrong row stays visible, which is the point: the next reader needs to
recognize the mistake on sight, not find it silently gone.

**Log at the moment, not at the summary.** A row written three hours later with a guessed
timestamp is worth much less than one written when the thing happened. `When` is when the change
happened; `Logged at` is automatic. The gap between them is itself a quality signal.

## The schema, and the two fields that carry the weight

| Field | Notes |
|---|---|
| `What` | One line, past tense, specific enough to act on. *"Closed C-07 on owner determination"*, not *"updated conflicts"*. |
| `When` | When it happened. Include the time. |
| `Actor` | Who made the change |
| `Logged by` | Who physically wrote the row |
| `Surface` | The thing that moved — page, database, repo path, Routine prompt, live service |
| `Action` | Created · Updated · Superseded · Decided · Flagged · Withdrawn · **Checked — no change** |
| `Provenance` | **Verified from source · Stated by owner · From record · Inference** |
| `Ref` | PR, commit, page or run link |
| `Logged at` | Automatic |

### Provenance — why it is a column and not a habit

Every documentation failure found on 2026-09-15 was the same failure: **provenance collapse.** A
hypothesis written in a code comment was read later as established fact, and propagated into three
separate records. Nobody lied; each step was a reasonable reading of the step before it. By the
time it mattered, the claim looked identical to a verified one.

Prose cannot prevent this, because prose lets you skip the distinction when you're in a hurry — and
you are always in a hurry. A required field cannot be skipped. **The moment of writing is the only
moment the distinction is cheap**; afterwards, recovering it means re-doing the original work.

So: `Verified from source` means you read the primary thing. `Stated by owner` means Todd said so.
`From record` means another document says so — *and you have not checked it*. `Inference` means you
reasoned to it and it is not confirmed. When a row mixes provenance, split it into two rows.

### Checked — no change

**"I looked and nothing moved" is information**, and it is precisely the information a derived
feed cannot contain — git log, page history and run records only show changes, so a check that
found nothing leaves no trace anywhere.

It is what stops the next agent repeating the check. Log one whenever someone else might
reasonably re-do the work: a live service verified, a surface swept and found unchanged, a
suspected problem investigated and found absent. Do not log routine internal reads — the test is
*"would a peer otherwise repeat this?"*

## Todd's rows, written by agents

Todd's decisions are logged **by the agent he stated them to**, with `Actor: Todd` and
`Logged by: <agent>`. Without this the feed has a hole exactly where the highest-stakes changes
happen — on 2026-09-15 the ownership partition, the SpinupWP decision and the hosting topology were
all his, and none would have appeared.

The two-field split is what keeps it honest: the row never claims Todd wrote it. If he disagrees
with how a decision was recorded, he appends a superseding row — he does not edit the agent's.

## Standing amendment to the ownership partition — needs Todd's confirmation

The partition of 2026-09-15 gives ScrumMaster **four** surfaces and says "nothing else, ever."
The Change Feed is a fifth thing it writes to.

This is recorded as an amendment rather than an exception, because it is a different *kind* of
write: the partition governs **ownership of content**, and the feed is **a record of events**, with
no content to own. Appending a row makes no claim on anyone's surface, and the never-edit-another's
-row rule preserves the property the partition exists to buy — that the owner of a thing can trust
it says what they left there.

**All three parties append. Nobody owns the feed's contents; each party owns its own rows.**

Todd has not separately confirmed this reading. Until he does, it is Claude Code's interpretation
of his instruction — `Provenance: Inference`, and flagged here rather than assumed.

## Where it is

| | |
|---|---|
| Database | *SystemOne Memory & Context Repository* → **📟 Change Feed** |
| Seeded | 2026-09-15, backfilled with that day's 19 changes from this session's own record |
| This protocol | `screme/screme-web` → `docs/change-feed-protocol.md` |
