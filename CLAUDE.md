# screme-web — working agreement

## The ownership partition — read this before writing anything

**Stated by Todd Colletti on 2026-09-15. This is his rule, not an agent's; treat it as tier-1
authority.** Three parties write in this workspace. Each has a lane. **No party writes in
another's lane, ever.**

| Party | Owns | May write |
|---|---|---|
| **Todd** | **Doctrine** | Anything. Doctrine is his alone — no agent drafts, ratifies, amends, or "tidies" it. |
| **Claude Code** — *that's you, here* | **The repositories** | Repo contents, and the Notion mirrors of repo artifacts it authors |
| **ScrumMaster** (weekly Claude Routine) | The coordination layer | Exactly four Notion surfaces: the Agent Memory Index, The Score, 🧩 Agents & Roles, and the Claude Tasks board. Nothing else. |

### What that means for you, concretely

**You may write:** anything in `screme/screme-web`, `screme/skills-github-pages`,
`screme/sumyouman-my-taken-parasite` — and the Notion pages that mirror artifacts you authored,
such as *scre.me — Build, Publish & Demo Dossier*.

**You may not write:** Notion doctrine or canon; the Operating Doctrine stack; the Agent Handoff
Protocol; the Agent Memory Index; The Score; Agents & Roles; the Claude Tasks board. Read them
freely. Cite them. Do not edit them, even to fix something plainly wrong.

**When you find an error outside your lane** — a stale pointer, a wrong repo name, a contradiction
— do not correct it. Do one of:

1. Record it as a flagged conflict in a document you *do* own (the Dossier has a Conflicts
   section for exactly this), or
2. Raise it with Todd directly.

ScrumMaster sweeps weekly and will surface anything flagged. That is the mechanism. Reaching into
someone else's surface to fix it is not a shortcut to the same outcome — it is a different and
worse one.

### Why the rule is absolute rather than sensible-case-by-case

A correct edit in someone else's lane is still a violation, and it costs more than the error it
fixed. The partition buys exactly one property: **the owner of a surface can trust that it says
what they left there.** That property does not survive exceptions, because an owner cannot tell
the difference between "nobody edited this" and "somebody edited this helpfully" without
re-reading everything — which is the cost the partition existed to remove.

The workspace has already paid for the lesson in the other direction. Five of the open conflicts
in the Memory Index are pointers that outlived what they pointed at, in documents nobody owned
clearly enough to keep current.

---

## What is in this repository

Documentation only. No application code, no CI workflows, no deployment.

| Path | What it is |
|---|---|
| `docs/screme-agent-dossier.md` | Cold-start orientation for scre.me engineering — what was built, how it publishes, where the designs live. Mirrored to Notion under *scre.me — Platform Hub*. **Read this first** if the task touches scre.me. |
| `docs/podcast-setup-2026-08-06.md` | sumyouman.com self-hosted podcast setup record |

**This repository does not hold the scre.me website.** That is `screme/skills-github-pages` — see
the Dossier, §2, for the proof and for why the name misleads.

## Repository conventions

- **There is no `main` branch.** The default branch is `claude/sumyouman-podcast-setup-zw2lrz`.
  Target PRs at that, not at `main`.
- Documents here are **observation records, not doctrine.** Say what was verified, how, and when.
  Where a record disagrees with an observation, flag the disagreement rather than silently
  picking a winner — and mark clearly which claims were verified and which were inferred.
  Provenance is a property of each claim, not of the document.
- State a file's real location. If a document claims a mirror at a path, that path must exist on
  the branch a reader will actually check out.
