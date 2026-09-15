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
| `Logged at` | Automatic (created time) |
| `Supersedes` / `Superseded by` | Self-relation. Points at the entry this invalidates, and back |

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

The partition of 2026-09-15 gives ScrumMaster a fixed allowlist of owned surfaces — **five**, since
Todd extended it from four on 2026-09-15 to cover the scre.me Live State page (C-12). The Change
Feed is a further thing it writes to, outside that list.

This is recorded as an amendment rather than an exception, because it is a different *kind* of
write: the partition governs **ownership of content**, and the feed is **a record of events**, with
no content to own. Appending a row makes no claim on anyone's surface, and the never-edit-another's
-row rule preserves the property the partition exists to buy — that the owner of a thing can trust
it says what they left there.

**All three parties append. Nobody owns the feed's contents; each party owns its own rows.**

**Still unratified — and deliberately not swept up with the rest.** On 2026-09-15 Todd ratified
the stopping rule, the conflict-number allocator and the Feed's rolling-window filter. He did not
name this amendment, so it remains Claude Code's interpretation of his instruction —
`Provenance: Inference`, flagged rather than assumed. Three of four is not four, and treating a
list as longer than it was is precisely the drift this file exists to catch.

## The bar — what gets a row, what gets a card

**Proposed by ScrumMaster, 2026-09-15. Ratified by Todd the same day. In force for all parties.**

> A finding gets a **Feed row and nothing else**, unless it **changes what someone does**. Then a
> card.

The failure this prevents is not missed findings — it is a board filling with true, correct,
unactionable observations until nobody reads it, at which point the real cards are invisible too.
**Precision has no natural stopping point.** The bar is the stopping point.

ScrumMaster applied it to its own output first: of eight cards opened on 2026-09-15, **it judged at
least two would not survive the test.** Claude Code is under the same bar — a verified detail that
changes no one's next action belongs in the log, not in a queue.

*Worked example, same day:* Claude Code mis-stated which option Todd took in closing C-12, calling
it a fifth when it was the fourth listed in its own document. True correction, real record, and it
changes nobody's next action — so it was acknowledged in conversation and **deliberately not
logged**. The bar's first live application was to a finding about the bar.

## Where it is

| | |
|---|---|
| Database | *SystemOne Memory & Context Repository* → **📟 Change Feed** |
| Seeded | 2026-09-15, backfilled with that day's 19 changes from this session's own record |
| This protocol | `screme/screme-web` → `docs/change-feed-protocol.md` |

---

## The read protocol

A log that is written and never read is worse than no log, because it feels like coverage.

1. **On waking, read before acting.** Open the **"Newest first — read this"** view and read down
   until you reach something you already know. That is your delta.
2. **Record your own last-run time** somewhere durable in your own lane — a Routine prompt, a repo
   file, a run report. The feed cannot tell you when *you* last looked.
3. **Then act.** ScrumMaster's charter makes this step 1 of its run, explicitly so it stops
   re-deriving what it could have read.

## The derived cross-check — how the log audits itself

A log is only trustworthy if you can tell when someone wrote *without* logging. Notion exposes
`page_last_edited_at` on every page, so the check costs one comparison per surface:

> For each memory surface, compare its `page_last_edited_at` against the newest Change Feed row
> naming it in `Surface`. **A page edited more recently than its newest feed row means someone
> wrote without logging.**

The feed side of that comparison:

```sql
SELECT "Surface", MAX("date:When:start") AS newest_entry
FROM "collection://62f4043e-3144-4d0f-bd9f-ee1d17c742e4"
GROUP BY "Surface" ORDER BY newest_entry DESC;
```

Run it as part of any sweep. **This is an audit, not a replacement:** derivation alone cannot record
who, why, provenance or intent, and it cannot see the repositories at all — which is exactly the
half of the world that cost this workspace real time on 2026-09-15.

---

## Allocating shared conflict numbers — the one real contention case

**Raised by ScrumMaster, 2026-09-15.** The conflict series `C-01…` is shared across two documents:
ScrumMaster holds C-01 to C-10 in the Agent Memory Index; Claude Code raised C-11 and C-12 in the
scre.me Dossier. **Two parties assigning from one sequence, with no allocator.** It has not
collided yet purely by luck.

Everything else in this workspace is partitioned by owner, so this is the only place two parties
genuinely contend. It does not need a lock either.

### The rule

**Claim a number by appending a Feed row first, then use it.**

1. Read the Feed for the highest claimed `C-nn`.
2. **Append a row claiming the next one** — `Action: Created`, `Surface: Shared conflict series`.
3. Only then write the conflict into your own document.

The claim is a row CREATE, never an UPDATE, so there is no read-modify-write and nothing to
clobber — the same property that made append-only the right shape for the Feed itself.

### What this actually buys — stated precisely

ScrumMaster's proposal said two agents "can't take the same slot without one seeing the other's
row." **That holds only if the second agent reads after the first has appended.** Two agents that
both read `max = C-12` before either appends will both append a claim to C-13, and both appends
succeed, because creates do not collide.

So the honest property is **detection, not prevention.** What changes is the failure mode, and the
change is large: today a duplicate number is *silent and permanent* — two documents each believe
they own C-13 and nothing in the workspace can tell. Under the rule it is **a visible duplicate in
one queryable place**, with a deterministic fix:

> **Tie-break:** the claim with the earlier `Logged at` keeps the number. The later claimant
> renumbers and appends a `Superseded` row pointing at its own withdrawn claim.

`Logged at` is server-set and monotonic, so the tie-break needs no coordination and both parties
reach the same answer independently.

A true allocator would need compare-and-set, which the Notion API does not offer. This is the best
available shape, and at two parties working at human pace the residual window is negligible — but
it is a window, and the protocol says so rather than claiming a guarantee it does not have.

### Status

**Claimed so far:** C-01 … C-12 in use (C-12 closed 2026-09-15); **C-13 claimed** (2026-09-15,
Claude Code, unused).

### The workspace's characteristic failure is identity, not logic

**ScrumMaster's diagnosis, 2026-09-15**, and it reframes every conflict on record. Count them:
stale collection IDs (C-01), wrong repo of record (C-08), wrong mirror path (C-09), wrong host
(C-07, C-11), and now a surface name that does not group. **Five instances in one day of the same
underlying fault** — not one of them a reasoning error. Every one is *a name that no longer
resolves to the thing it names.*

That is worth holding onto, because it predicts where the next failure will be: not in an argument,
in an identifier.

**The structural fix, deferred by agreement.** A naming convention is the weak form — it depends on
every writer remembering. Making `Surface` a **select or a relation** instead of free text is the
form that cannot rot, because an unrecognized value becomes impossible rather than merely
unlucky.

*The tension to resolve before building it:* a **select** is a closed set someone must maintain,
and surfaces appear constantly — repo paths, live services, Routine prompts — so it either blocks
writers or grows unbounded. A **relation** to a Surfaces registry is the durable shape, but that
registry is itself a new shared surface needing an owner, which is C-12 over again. Neither is
obviously right, which is why it is not tonight's work.

### Name surfaces consistently, or the cross-check fragments

Running the audit query on 2026-09-15 showed the same logical surface splitting into separate
groups because writers spelled it differently — an em dash versus a hyphen, and Notion
auto-linking a domain inside the text so `scre.me` became `[scre.me](http://scre.me)`. Grouped by
a raw string, those are three surfaces, not one, and the audit under-reports.

**Convention:** write `Surface` as a plain identifier — no auto-linked domains, a hyphen rather
than an em dash, and the same spelling a previous row used. Check the newest rows for an existing
spelling before inventing one. *Found by running the cross-check, not by reasoning about it.*

**Ratified by Todd, 2026-09-15.** In force for all parties. The Feed row recording its adoption
carries `Provenance: Inference` because that was true when it was written; the ratification is a
separate row at `Stated by owner`, rather than an edit to the first — the append-only rule applied
to the protocol's own history.

---

## Deviations from the specification — recorded, not buried

The build differs from the card's schema in three ways. Each was deliberate; none was silent.

**1. `When` is a manual date; `Logged at` is the automatic one.** The spec said `When` should be the
created time, never set by hand. Splitting them was necessary the moment agents log Todd's
decisions for him — a decision stated at 07:15 and written at 08:30 needs both times, or the feed
mis-times every owner decision and every backfilled row. The automatic timestamp is preserved as
`Logged at`, and **the gap between the two is itself a quality signal**: a large gap means a change
was recorded late. *Deviation favouring accuracy over the spec's simplicity — worth Todd's glance.*

**2. `Actor` is a third field, alongside `Logged by`.** The spec reasoned that `Logged by` plus
`Provenance: stated-by-owner` already encodes whose decision it was. True, but it makes "every
decision Todd made" a two-field query and leaves `Actor` implicit. An explicit `Actor` column makes
attribution readable at a glance and filterable on its own. *Additive, not contradictory.*

**3. The rolling-window view could not be built through the API.** Notion's view DSL accepts only
fixed ISO dates, not relative ranges, so a "past 30 days" filter would have been a hard-coded date
that silently goes stale — precisely the failure mode this workspace keeps paying for. A sorted
**"Newest first — read this"** view was created instead. **Notion's own UI does support a relative
filter**, so *When · is within · the past month* was applied to that view by Todd in the UI on
2026-09-15. **The deviation is closed** — the limit was the API's, not Notion's, and naming which
one it was is the difference between a gap someone can close and a gap that looks permanent.

## Status against the card

| Deliverable | State |
|---|---|
| Database under SystemOne | Done |
| Full schema incl. `Supersedes` self-relation | Done |
| `Provenance` required, four values | Done |
| `checked-no-change` as a first-class action | Done |
| Protocol page — write and read rules | Done (this file) |
| Derived cross-check | Done — documented above, query included |
| Backfill | Done — 2026-09-15, 19 rows |
| ScrumMaster wired to append and to read first | Done — in its Routine prompt |
| Rolling-window filtered view | **Done** — applied by Todd in the Notion UI, 2026-09-15 |
| Registration in the Agent Memory Index | **Not mine** — ScrumMaster's lane, on its next sweep |
