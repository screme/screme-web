# scre.me — Build, Publish & Demo Dossier

**Purpose.** A cold-start orientation for any agent picking up scre.me work. It answers three
questions that currently cost every new agent a discovery pass: *what was built*, *how it reaches
the public*, and *where the designs and demos live*. Compiled 2026-09-15 by a Claude Code session
with all three repositories checked out and the live site read directly.

**Authority.** This is an **observation record**, not doctrine. Everything below was verified from
source — repository files, Git history, and HTTP responses from `https://scre.me`. Where a
recorded document disagrees with what was observed, the disagreement is flagged in §5 and left
unresolved. Mirrored to Notion under *scre.me — Platform Hub*.

**Where this file lives, stated precisely.** It is on branch `claude/confident-feynman-vks46o`,
open as **draft PR screme/screme-web#1** against this repo's default branch
(`claude/sumyouman-podcast-setup-zw2lrz` — this repository has no `main`). **Until that PR is
merged the file does not exist on the default branch**, so an agent sent to
`screme-web/docs/screme-agent-dossier.md` on the default branch will not find it. The Notion page
is the reliable pointer in the meantime. Flagged by ScrumMaster on its 2026-09-15 run 02 as the
same failure mode as C-09, one repository over — and it was right to.

---

## 1. Orientation in one paragraph

scre.me is the **thesis property** in Todd Colletti's portfolio: an AI-augmented IP orchestration
company whose public site is itself the showcase artifact. The site was designed in **Claude
Design** and built and hardened in **Claude Code**. It is a static, self-contained site — no CDN,
no external fonts, no third-party runtime requests — of eleven pages plus two substantial
interactive prototypes. It publishes to an **nginx droplet** via **GitHub Actions**. The
commercial wedge it sells is **character-led voice agents for hospitality venues**; **FirstCall**
is the owner-facing app for running them.

---

## 2. What was built — the site

**Live source of truth: `screme/skills-github-pages`** (default branch `main`).

This is the single most important fact in this dossier, because two other repositories look like
candidates and are not. **Proof (2026-09-15):** the body served at `https://scre.me/` and
`index.html` in `skills-github-pages` are the same 19,982 bytes with the same SHA-256,
`2b8f1df93bdd4521…` — not merely similar, identical. Served by `nginx/1.24.0 (Ubuntu)`.

> The repository name is an accident of history — it began as a GitHub Pages training exercise on
> 2026-07-14 (`README.md` still carries the exercise text and Mona the Octocat). The real site was
> built on top of it starting 2026-07-26. Do not be misled by the name or the README.

### Pages (all verified HTTP 200)

| Page | Role |
|---|---|
| `index.html` | Landing. Four-pillar thesis, three-stage plan (Wedge / Engine / Upside) |
| `venue-agents.html` | The product. Character-led voice agents; design-partner program |
| `firstcall.html` | FirstCall — the owner's app. Full interactive prototype |
| `live-demo.html` | Three interactive demos in one page (see §4) |
| `platform.html` | The orchestrator, described precisely |
| `territories.html` | Eight territories, one active |
| `investors.html` | The investment case |
| `rights.html` | Human-authorship and chain-of-title protocol |
| `community.html` | Community principles |
| `press.html` | Boilerplate, fact sheet, assets |
| `request-briefing.html` | Five contact paths |

### Design system

**Nocturne** — a compact dark interface language shared across the site and the app prototypes.
Near-neutral blue-grey ground (`#161826`), a single blurple accent (`#9184d9`) used as line, tint
and glow but never as a flood, outlined rather than filled primary buttons, rules that fade to
transparent at their ends, contrast from tonal ramps rather than saturation, elevation as edge
plus ambient darkness. Headings hold weight 500; hierarchy comes from size and space.

Stylesheets: `assets/screme.css` (site), `assets/nocturne.css` (design system),
`assets/firstcall.css`, `assets/tip-ledger.css`.

### Standing engineering policy

**No external requests.** Inter is self-hosted, React is vendored locally, and image assets in the
prototypes are drawn SVG scenes encoded as `data:` URIs rather than files. This is deliberate — an
outage at a CDN cannot blank the site, and there is no third-party beacon on a page shown to
investors.

### Build timeline (from Git history)

| Date | Milestone |
|---|---|
| 2026-07-14 | Repo created as a GitHub Pages exercise |
| 2026-07-26 | Site integrated: pages + the interactive operator console; Tip Ledger demo added |
| 2026-07-27 | Repositioned for fundability — wedge-first structure, evidence labels, five audience funnels; real logo site-wide; **auto-deploy workflow added** |
| 2026-08-06 | Deploy verification hardened |
| 2026-09-01 | **FirstCall merged as demo 02**; six-hourly drift guard added; SpinupWP conflict recorded |
| 2026-09-03 | FirstCall Analytics screen added; two stale evidence rows corrected |

---

## 3. How it publishes — GitHub → DigitalOcean droplet

**Workflow: `.github/workflows/deploy-droplet.yml`** in `screme/skills-github-pages`.
**Manual equivalent: `deploy/deploy-to-droplet.sh`** for a machine with SSH access.

### The path

1. **Triggers** — push to `main` touching `*.html`, `assets/**`, or the workflow itself; a
   six-hourly schedule (`17 */6 * * *`); or manual `workflow_dispatch`. Concurrency group
   `deploy-droplet`, cancel-in-progress.
2. **Gate** — the job is skipped entirely unless the `DROPLET_HOST` repository *variable* is set,
   so the workflow is inert rather than broken in a fork.
3. **SSH** — private key from the `DROPLET_SSH_KEY` *secret*; host key pinned via `ssh-keyscan`.
4. **Web-root detection** — *behavioral, not guessed*. If `DROPLET_WEBROOT` is unset, the workflow
   writes a uniquely-named probe file into every candidate directory under `/var/www`, `/srv` and
   `/usr/share/nginx` that contains an `index.html`, then asks nginx over HTTPS which one it
   actually serves. Probes are deleted immediately. Falls back to the first `root` directive in
   `sites-enabled`.
5. **Rsync** — `./*.html` and `./assets` only, **with no `--delete`**. Server-side files that are
   not in the repo (`legal.html`, `privacy.html`, `terms.html`, the old prototype runtime) survive.
6. **Verify** — fetches `https://scre.me/` and fails the job unless the hero string *"Original
   properties for audiences"* is being served. A deploy into a directory nginx never reads fails
   loudly instead of reporting success.

### Required repository configuration

| Kind | Name | Notes |
|---|---|---|
| Secret | `DROPLET_SSH_KEY` | Private key with droplet access |
| Variable | `DROPLET_HOST` | **Required** — the job skips without it |
| Variable | `DROPLET_USER` | Defaults to `root` |
| Variable | `DROPLET_WEBROOT` | Optional — auto-detected when unset |

### The droplet itself — verified from deploy logs, 2026-09-15

Read from run 64 of the deploy workflow (06:44 UTC), which prints what it finds:

| Fact | Value |
|---|---|
| Host | `143.198.130.132` — DigitalOcean |
| OS | Ubuntu 24.04 (`OpenSSH_9.6p1 Ubuntu-3ubuntu13.19`) |
| Web server | `nginx/1.24.0 (Ubuntu)` |
| Web root (auto-detected by probe) | **`/var/www/screme/public`** |
| Deploy user | `root` |
| Payload | 11 HTML files + 8 assets, 363,496 bytes; verify step passed |

**This is not a SpinupWP-managed server.** SpinupWP's convention is `/sites/<domain>/files/`, and
this project's own `DEPLOYMENT.md` confirms that shape by citing
`/sites/scre.me/files/briefing-requests.jsonl`. The live server uses a conventional
`/var/www/…` layout and accepts `root` over SSH, which SpinupWP disables in favour of per-site
users. A conventional Ubuntu + nginx droplet, in other words — consistent with the owner's
statement that the SpinupWP path was declined.

*Limit of this evidence:* it is inferred from directory layout and login user, not from logging in
and inspecting for a control-panel agent. It rules out the specific SpinupWP Git-deployment
writer; it does not by itself prove that no other writer (a cron, another CI) exists.

*Noted in passing, not a finding:* deploys run as `root` with a key held in repository secrets.
Workable, but a dedicated deploy user owning only the web root would be the smaller blast radius.

### Why the drift guard exists — read this before touching deployment

The six-hourly schedule is not belt-and-braces; it is scar tissue. The workflow's own comments
record the incident history:

- **2026-08-06** — the web root was overwritten with an older export **73 seconds** after a
  verified-good deploy. The site served stale content for weeks because nothing re-checked it.
- **2026-09-01** — it recurred three more times, and the cause was identified: the scre.me site in
  **SpinupWP also has a Git deployment** pointed at `screme/sumyouman-my-taken-parasite`, serving
  that repo's `public/` directory into the same web root. Two writers, one destination.
  - `17:19` — that repo's PR #4 merged → `index.html` replaced
  - `18:33` — restored by the drift guard, unaided
  - `18:42` — this workflow deployed FirstCall; verified good
  - `19:15` — that repo's PR #7 merged → web root **replaced, not merged**: every page and asset
    unique to this site returned 404

The 19:15 event *deleted* rather than overlaid, which means an out-of-band overwrite can take the
site **down**, not merely age it. The drift guard repairs it within hours, but it treats the
symptom.

> **Standing operational rule, from the workflow itself:** until the second writer is removed,
> **treat any merge in `sumyouman-my-taken-parasite` as an outage on scre.me.**
>
> **The fix:** SpinupWP → the scre.me site → Git deployment → **disable**. This is an owner action
> in an authenticated console; no agent has performed it as of 2026-09-15.

---

## 4. Where the designs and demos live

### FirstCall — canonical design source

**`screme/sumyouman-my-taken-parasite`, `docs/firstcall/`.** This is the design record for the
administrative app, and the source of truth for a native build.

| File | What it is |
|---|---|
| `handoff.md` | The full specification — tokens, information architecture, every screen, states, copy, open questions. ~28 KB. **Source of truth for the native build.** |
| `nocturne-design-system.md` | The Nocturne design system readme |
| `FirstCall.dc.html` | The original Claude Design canvas as authored |
| `FirstCall-Demo.dc.html` | The original demo canvas — explainer beside a phone frame |
| `README.md` | How the canvas relates to the live web demo |

**What FirstCall is.** An ElevenLabs conversational agent answers a venue's inbound line over
Twilio VOIP, carrying that venue's own personality and operating protocol. FirstCall is the
**mobile app venue management runs it from**: tune each room's personality, edit the knowledge the
agent may answer from, set what it must never handle alone, work the queue of what it could not
close, and manage numbers, routing, plan, notifications, team and branding — across several venues
under one parent group.

- Tagline: *"Hospitality that answers the call."*
- Brief: **iOS and Android.** The prototype is framed iOS (390 × 844, five-item bottom tab bar).
- Tab bar: **Tonight · Calls · Inbox · Agent · More**
- Not yet designed: new-user registration, venue registration, and the "What is FirstCall?"
  overview. These raise a toast in the prototype.

**Critical note for whoever builds it:** the `.dc.html` files and the web port are *design
references*, not production code. The single-file HTML component with an inline-styled React-like
template is an artifact of the design tool, not a recommended architecture. Recreate the designs in
the target codebase's own patterns and wire to the real ElevenLabs/Twilio data model — every venue
name, transcript and number in the prototype is illustrative.

### The web prototypes — `screme/skills-github-pages`

`live-demo.html` (~61 KB) carries **three demos in one page**, all driven by sample data, all
entirely client-side — no call is placed, no account created, nothing leaves the page:

1. **The operator console** (`#demo-console`) — the console behind the voice. Live call list,
   transcript with typing and waveform, answer-source attribution, takeover control, knowledge and
   lost-&-found views, guardrail evidence, and an overview with calls-per-day, request-mix donut,
   hours distribution and latency trace. Scenario: *"Sunday, 4:12pm — beer bust."*
2. **FirstCall** (`#demo-firstcall`) — the owner's app, ported from `FirstCall-Demo.dc.html`.
3. **Tip Ledger** (`#demo-tip-ledger`) — eight iOS screens for tip reconciliation; the app the
   venues close out on.

`firstcall.html` (~13 KB + `assets/firstcall.js`, ~67 KB) is the full-page FirstCall prototype.
Both prototypes drive **every screen from a single state object**, so moving a personality slider
or clearing a follow-up recomputes the rest of the app. The iPhone bezel is a presentation device,
not the product.

### Other design and reference material

`screme/sumyouman-my-taken-parasite/docs/` also holds `active-memory.md` (positioning and
operational state, mirror of the Notion Platform Hub), `technical-framework.md` (how the site is
built and what was hardened), and `ip-schedule.md`.

---

## 5. Conflicts — flagged, not resolved

**C-07 — CLOSED by owner determination, 2026-09-15.** The question was whether Hetzner/SpinupWP
was abandoned for DigitalOcean, or whether both exist for different properties.

**Todd's answer, stated directly and recorded here as authoritative:** *"Hetzner/SpinupWP is old
data, which we selected against proceeding with."* scre.me runs on **DigitalOcean droplets**.
Hetzner and SpinupWP were evaluated and declined. `DEPLOYMENT.md` in
`sumyouman-my-taken-parasite` is therefore **superseded, not parallel** — it documents a path that
was never adopted, and no agent should provision from it.

**The operational follow-up is now answered too.** The question was whether anything besides the
GitHub Actions workflow can still write to the droplet's web root — specifically the SpinupWP Git
deployment named in the 2026-09-01 incident log. Deploy-log evidence (§3, *The droplet itself*)
shows the live server is a conventional Ubuntu + nginx droplet at `/var/www/screme/public`, not a
SpinupWP-managed one. **The 2026-09-01 second writer is not present on the server serving scre.me
today.** The incident log is history, not a live condition — most plausibly a record of the
transition off the declined SpinupWP path, though that sequencing is inference, not established.

*Residual, and small:* this rules out the SpinupWP writer by the server's shape; it does not prove
no other writer exists. The last 12 scheduled runs (2026-09-12 → 2026-09-15) all succeeded and the
live site was correct at 06:53 UTC on 2026-09-15.

**C-08 — the repo of record is recorded wrong in three places.** The Platform Hub names
`sumyouman-my-taken-parasite` as the deploy repo. ScrumMaster's Live State page names `screme-web`.
`active-memory.md` names the branch `claude/scrme-design-implementation-x4pbuf` in
`sumyouman-my-taken-parasite`. **The live site is served from `skills-github-pages`**, proven by
byte-exact content match. All three records predate the move and none of them names it.

**C-09 — a documented mirror does not exist.** Notion states that scre.me Active Memory is
"mirrored in the repo at `docs/active-memory.md`". That file exists in
`sumyouman-my-taken-parasite/docs/`, **not** in `screme-web/docs/`, which at the time of writing
contained only `podcast-setup-2026-08-06.md`. An agent told to read `docs/active-memory.md` in
`screme-web` will find nothing and may conclude the memory is missing.

**C-10 — `active-memory.md` positioning has drifted from the live site.** The document describes
the thesis as *"Stigma is whitespace / IP is the beachhead / AI collapses the cost / The property
is alive"* with a hero of *"Whole franchises, generated. Aimed where only the bold dare to tread."*
The live site's four pillars and its hero — *"Original properties for audiences traditional studios
overlook"* — are a softer, more fundable register. Both are on record; the site is the newer of
the two. The brand promise *"only those you want will hear"* is carried verbatim in both.

**C-11 — CLOSED by owner determination, 2026-09-15.** `active-memory.md` lists sumYOUman as
sharing "the Hetzner/SpinupWP server (WordPress, Vice theme)". That line is **wrong**, and the
owner has confirmed the correct arrangement (see §8). Headers read on 2026-09-15 agree:
`sumyouman.com` returns `host-header: WordPress.com` with an Automattic `x-hacker` header and an
`_atomic_dca` cache marker; `scre.me` returns a bare `nginx/1.24.0 (Ubuntu)`. **Two separate
stacks, no server in common.** The `active-memory.md` line remains uncorrected at source — it is
an indexed document, flagged here rather than edited.

*Clarification, since the naming invites the error:* SpinupWP is a **server control panel**, not a
host and not WordPress. It manages a VPS rented elsewhere. It has no relationship to
WordPress.com, which is a managed platform run by Automattic. Nothing about the scre.me stack
involves WordPress at any point — it is eleven static HTML files and a folder of assets.

**C-13 — ScrumMaster's Routine prompt still says four surfaces.** Todd extended ScrumMaster's
allowlist from four surfaces to five on 2026-09-15, adding the scre.me Live State page and
closing C-12. The Routine record has not been modified since **`2026-09-15T08:24:26Z`**, and its
prompt still reads *"Exactly four surfaces,"* still lists four, and still carries a section
headed *"Consequence you must handle on your next run"* instructing ScrumMaster to stop writing
to the Live State page and open a card for a decision Todd has already made.

*On the timing, precisely:* the Feed row recording the extension carries `When` = 09:02 UTC but
was itself created at 08:45:21 UTC. A row cannot precede the event it records, so 09:02 is a
narrative timestamp, not an observed one, and **08:45:21 is the hard upper bound on when the
decision was made.** The prompt therefore predates the extension by *at least* 21 minutes — and
has stood unrevised through the six days since. The exact gap is not established and does not
need to be; the ordering is what carries the conflict.

ScrumMaster runs memoryless. The prompt is the whole of what it knows at the start of a run, so
this does not decay quietly: every future Monday it will orient itself to a four-surface
partition and re-raise a closed question. Its own Change Feed row records the extension, but a
row it has not read yet cannot correct a prompt it is already running under.

**Verified from source 2026-09-21** by reading the Routine record directly (`updated_at`, prompt
text, allowlist of four) and both timestamps on the extension row. **Flagged, not fixed** — the
Routine's configuration is not this agent's lane, and a prompt is rewritten only when Todd asks
for it.

---

## 6. Open items for the owner

These wait on the owner — most on authenticated consoles no agent holds, one (item 6) on his
explicit say-so rather than on access. None is blocked on engineering.

1. ~~Disable the SpinupWP Git deployment.~~ **Resolved** — the live droplet is not SpinupWP-managed
   (§3). No action needed. The standing "treat any merge in `sumyouman-my-taken-parasite` as an
   outage" rule in the workflow comments is now stale and can be retired next time that file is
   touched.
2. **Confirm the DigitalOcean droplet inventory** — count, sizes, regions, attached resources.
   Project console: `5c6da413-24b7-4e57-b100-cd8cee6f0ec0`. The scre.me droplet itself is now
   identified (§3); what else sits in that project is not.
3. **Reconcile the deployment runbook** — `DEPLOYMENT.md` should either be superseded by the
   droplet workflow or scoped explicitly to a different property.
4. **Decide the FirstCall / Venue Agents altitude.** Todd states FirstCall is Bruce productized;
   the site presents FirstCall as the owner's console and the character agent under Venue Agents.
   A reader currently has to guess which is the product.
5. **Choose the native stack for FirstCall** — `handoff.md` is complete and waiting.
6. **Update ScrumMaster's Routine prompt to five surfaces** (C-13). The prompt predates your
   2026-09-15 extension and still orders a card for the decision that extension made. This one is
   not console-bound — an agent can edit it with `update_trigger` — but a Routine's prompt is
   rewritten only on your explicit say-so, so it waits on you.

---

## 7. Cross-references

| Surface | Where |
|---|---|
| Live site | `https://scre.me` |
| Site source (canonical) | `screme/skills-github-pages` |
| FirstCall design handoff | `screme/sumyouman-my-taken-parasite` → `docs/firstcall/` |
| Deployment workflow | `skills-github-pages` → `.github/workflows/deploy-droplet.yml` |
| Notion — Platform Hub | *scre.me — Platform Hub (Website, Deployment & Active Memory)* |
| Notion — Live State | *scre.me — Public Site, Repo & Deployment (Live State)* |
| Notion — Memory Index | *SystemOne — Agent Memory Index* |
| Bruce / SF Eagle lineage | Notion — *🤖 Bruce — SF Eagle Phone Agent (ElevenLabs)* |

---

## 8. Infrastructure topology — owner determination, 2026-09-15

Stated directly by Todd and recorded as authoritative. This supersedes any conflicting
description in older documents, which are flagged in §5 rather than corrected at source.

| Property | Host | Registrar | Notes |
|---|---|---|---|
| **scre.me** | DigitalOcean droplet, nginx | Namecheap | Static site, published by GitHub Actions (§3) |
| **sumyouman.com** | WordPress.com Pro Business | **WordPress.com** | Custom **ViceDrk** theme; media-rich and complex. Host *and* registrar — the platform's model effectively requires full integration to work well, so the domain sits with them deliberately. **This exception applies to sumyouman.com only.** |
| **All other domains** | — | Namecheap | Namecheap is the domain repository of record |

**Email and identity:** Google Workspace — Gmail and Google Apps.

**Declined:** Hetzner + SpinupWP. Evaluated, not proceeded with. Any document describing that
stack as the target is describing a path not taken.

**Aspirational, not adopted:** enterprise-level backend infrastructure, deferred until scale
justifies it. Not present in any current system; do not design against it.

---

## 9. The ownership partition — Todd's rule, 2026-09-15

Stated by Todd directly. **Tier-1 authority.** Three parties write in this workspace; each has a
lane; no party writes in another's lane, ever.

| Party | Owns | May write |
|---|---|---|
| **Todd** | **Doctrine** | Anything. Doctrine is his alone — no agent drafts, ratifies, amends or "tidies" it. |
| **Claude Code** | **The repositories** | Repo contents, and the Notion mirrors of repo artifacts it authors — **this page included** |
| **ScrumMaster** | The coordination layer | Five surfaces: Agent Memory Index, The Score, 🧩 Agents & Roles, Claude Tasks board, scre.me Live State — plus append-only Change Feed rows. Nothing else. |

**This page sits in Claude Code's lane.** ScrumMaster indexes it and does not edit it; that is
recorded on the Memory Index. Corrections to it come from a Claude Code session or from Todd.

**When any party finds an error outside its lane**, it does not correct it. It flags it — as a
conflict in a document it owns, or as a card on the board — and the owner decides. A correct edit
in someone else's lane is still a violation, and costs more than the error it fixed: the partition
buys exactly one property, that **the owner of a surface can trust it says what they left there**,
and that property does not survive exceptions.

### Where the rule is enforced

| Party | Enforcement point |
|---|---|
| ScrumMaster | Its Routine prompt — rewritten 2026-09-15 with the allowlist as an absolute, and a standing instruction to report lane violations, its own included |
| Claude Code | `CLAUDE.md` at the root of each repository. Present in `screme-web`; **not yet in `skills-github-pages` or `sumyouman-my-taken-parasite`** |
| Todd | Nothing to enforce — he owns doctrine and may write anywhere |

### Open consequence — flagged, not resolved

**C-12 — CLOSED by owner determination, 2026-09-15.** ScrumMaster created and wrote
*scre.me — Public Site, Repo & Deployment (Live State)*, which the partition's original four-surface
allowlist did not cover, leaving the page unowned. Todd resolved it by **extending the allowlist
from four surfaces to five**, the fifth being that page. ScrumMaster now owns it explicitly rather
than by accident. Nothing was deleted or migrated.

*How this record learned of it:* ScrumMaster appended the closure to the Change Feed at 09:02; this
document was stale for roughly four minutes and was corrected from that row — **which is the feed
doing the job it was built for**, one rung down from the problem it was built to solve.
