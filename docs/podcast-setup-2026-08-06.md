# sumyouman.com — Self-Hosted Podcast Setup (2026-08-06)

Self-hosted podcasting is live on sumyouman.com via **Seriously Simple Podcasting (Castos) v3.16.3**,
with a fully valid RSS feed that is submission-ready for Apple Podcasts and Spotify.

## The feed

**Canonical feed URL (submit this):**

```
https://sumyouman.com/feed/podcast/sumyouman/
```

- `https://sumyouman.com/feed/podcast` 301-redirects to the canonical URL, so either works.
- Validated with the W3C Feed Validator: **valid RSS**. The only warnings are benign
  (Google Play / SSP / WordPress namespaces the validator doesn't know, and a
  self-reference notice caused by validating via direct input instead of by URL).
- Published episodes carry a real `<enclosure url="….mp3" type="audio/mpeg" length="…">`;
  episodes without an audio file are automatically excluded from the feed by SSP.
- The episode MP3 serves with `Accept-Ranges: bytes` (HTTP 206 verified) — required by Apple.
- Note: the WordPress.com edge cache (30-min TTL set by the SYM Performance Tweaks plugin)
  applies to the feed, so newly published episodes can take up to ~30 minutes to appear
  to podcast apps. Harmless for podcast delivery.

## Channel configuration (Feed details, series term 83 "sumYOUman")

| Field | Value |
|---|---|
| Title | sumYOUman |
| Subtitle | Field transmissions from a para-normal rock band |
| Description | "Field transmissions from sumYOUman — a para-normal rock band documenting what happens when reality starts listening back. Human-written, AI-performed. That's not a disclaimer — it's the plot." (PROV-1 compliant) |
| Author / Owner | Stephen Todd Colletti |
| Owner email | press@sumyouman.com |
| Cover art | 3000×3000 JPEG (attachment 1440), resized from the "sumYOUman badge patch" asset (1393) |
| Category 1 | Music → Music Commentary |
| Category 2 | Fiction → Science Fiction *(added because the show is an audio drama — remove if unwanted)* |
| Language | en-US |
| Explicit | false (clean) — **confirm this is the intent** |
| Type | episodic, Season 1 |
| Copyright | © 2026 sumYOUman |

Options are stored as `ss_podcasting_*_83` (SSP reads per-podcast options with the series-term suffix).

## Episodes

The site already had a real show: **Signal Check**, built on the custom `sym_episode` post type
(registered by Code Snippets snippet #5, archive at `/signal-check/`). Instead of duplicating
content into SSP's `podcast` post type — which collides with the Vice theme's own demo `podcast`
type — SSP was configured to treat `sym_episode` as a podcast post type
(`ss_podcasting_use_post_types = ["sym_episode"]`). One canonical episode page per episode,
no theme conflict.

1. **Signal Check Episode 1** (post 1238) — **published, in the feed.**
   - Enclosure: `Sc-epi-1.mp3` (16:34, 15,928,097 bytes, audio/mpeg)
   - iTunes: episode 1, season 1, full, clean; episode art converted to 3000×3000 JPEG
     (attachment 1446, from IMG_0521.webp — Apple doesn't accept webp)
   - Description (also shown on the `/signal-check/` episode card): on-brand, includes the
     human-written/AI-performed provenance line and companion-listening links to the
     Parasite and I'm a Monster hyperfollow pages. Trim the card copy if it reads long.

2. **Sonic Range — Episode 112** (post 1441) — **DRAFT, not in the feed.**
   - Created from the `episode_112_v4_full.mp3` audio (6:25) uploaded during the session
     (byte-identical to media 1056 already in the library).
   - Wired as episode 2 with placeholder copy. **Publishing it puts it in the feed** —
     edit the title/description first.

## Other changes

- **Vice theme demo podcast posts drafted (not deleted):** PDCST 01 (406), PDCST 02 (411),
  PDCST 08 (428), PDCST 12 (432). They were never in the SSP feed, but this removes the fake
  episodes from the public `/podcast/` archive. Rollback: set status back to `publish`.
- SSP auto-created an "Episode List" page (1439) at `/ssp-podcast-archive/` — left published
  but unlinked; delete or use as you like.
- SSP player auto-injection disabled (`ss_podcasting_player_locations = []`) so the designed
  Signal Check pages are untouched.
- Rewrite rules flushed via the `sym_rewrite_v` option bump (now `1.3-ssp-feed`).
- New media: 1440 (show cover 3000×3000), 1446 (episode 1 art 3000×3000).

## Safety / verification

- Site is on WordPress.com Atomic hosting (host-managed automatic backups + Activity Log
  restore points) — verified via response headers before any changes.
- Post-activation smoke tests passed: homepage, `/mediakit`, the media-kit HTML file,
  `/signal-check/`, `/podcast/` — all 200, no PHP errors, media kit untouched.
- All changes are additive and reversible; nothing in the theme or unrelated plugins was modified.

## Remaining manual steps (require your accounts)

1. **Apple Podcasts Connect** — submit `https://sumyouman.com/feed/podcast/sumyouman/`
   at https://podcastsconnect.apple.com (needs your Apple ID).
2. **Spotify for Creators** — add the same feed at https://creators.spotify.com.
3. Confirm the **explicit flag** (currently clean/false) and the **category** choices.
4. Review/publish the **Episode 112 draft** and confirm the episode-card copy.
