# FCast Slint UI — Futures (applicability of Moblin UI)

This directory triages **every Moblin SwiftUI view file** in
`draft/moblin-ui/Moblin/View/` (279 files + `MainView.swift`) for whether it
applies to the FCast Android sender Slint UI.

The companion `phases/` directory contains the **executable plan** (Phases 0–11)
for porting the applicable subset. This `futures/` directory is the
**reference / triage record** that documents what is *not* being ported, and
why — so future contributors don't ask "did you miss `XYZView.swift`?".

## Files

| File | Purpose |
|---|---|
| [`README.md`](README.md) | This file — overview and exclusion taxonomy. |
| [`NOT-APPLICABLE.md`](NOT-APPLICABLE.md) | Per-file table marking each Moblin view as applicable / not applicable / deferred, with a one-line reason. |

## Applicability legend

Each Moblin view file is tagged with one of:

| Tag | Meaning |
|---|---|
| **applicable** | Has an FCast sender equivalent and is already covered in `phases/` (Phase 0–11). |
| **applicable-future** | Has an FCast sender equivalent but is intentionally out of scope for the v1 phase plan. Could be promoted to a future phase if/when the underlying Rust capability lands. |
| **not-applicable — chat** | Streamer chat (Twitch/Kick/YouTube IRC, chat overlays, chat bots, TTS). FCast is a media-cast protocol with no chat surface. |
| **not-applicable — navigation** | Streamer-broadcasting navigation overlay (location/route HUD). Not relevant to a cast remote. |
| **not-applicable — replay** | Live-stream instant replay (rewind during broadcast). Not applicable to a one-way cast sender. |
| **not-applicable — right-overlay** | Moblin's right-side broadcast HUD layout (beauty/face/pixellate/whirlpool/scene selector). The layout is excluded; *individual* generic widgets inside (e.g. `AudioLevelView`, `SegmentedPicker`) are tagged separately. |
| **not-applicable — streaming-platform** | RTMP / SRT / RIST / WHIP / RTSP / SRTLA / Belabox / Twitch / Kick / YouTube / Soop / OBS-remote ingest, server, and platform configuration. FCast uses its own binary protocol; these are unrelated. |
| **not-applicable — scene-widget** | Moblin scenes / widgets / overlays editor (alerts, scoreboards, slideshows, VTubers, PNGTubers, wheel-of-luck, bingo, browser-source widgets, video-source widgets, effects). Editorial broadcast tooling, not applicable to a cast remote. |
| **not-applicable — peripheral-hardware** | External hardware peripherals: DJI gimbals/devices, GoPro cameras, Tesla, BlackShark phone coolers, Cat printers, gimbals, selfie sticks, workout devices, game controllers. Out of scope for FCast sender. |
| **not-applicable — ios-target** | iOS/watchOS/macOS-specific targets: Apple Watch app, Mac key-press, external display, iOS deep-link creator. Android sender doesn't have these targets. |
| **not-applicable — iap** | In-app purchase / store. FCast Android sender does not monetize. |
| **not-applicable — moblin-internal** | Moblin-specific feature (Moblink relay, OpenAI integration, MetalPetal effects, etc.) with no FCast analogue. |
| **deferred — needs-rust-capability** | Has potential FCast applicability but blocked on Rust media-graph capability that doesn't exist yet (camera capture, local recording, Wi-Fi Aware peer discovery). Revisit after that capability lands. |

## Exclusion summary (per user direction)

The user explicitly excluded **four** Moblin UI categories from the FCast port:

1. **chat** — all chat surfaces, including TTS / Talkback
2. **navigation** — Moblin streaming-navigation overlays
3. **replay** — live-stream instant replay
4. **right-side overlays** — Moblin's broadcast right HUD layout

The `NOT-APPLICABLE.md` table treats these four as **permanently excluded**
(not "deferred"). Anyone wanting to add them must first revisit the v1 scope
decision documented in `draft/slint-ui/phases/PHASE-7-settings-pages.md`
(which already lists chat/scenes/widgets/ingests as "omitted by design") and
in `draft/slint-ui/README.md`.

## How to use this directory

- **When triaging a new feature request**, check `NOT-APPLICABLE.md` first to
  see whether the relevant Moblin view was excluded by category. If it was,
  the request is either out of scope or requires a scope-change discussion.
- **When promoting a `deferred — needs-rust-capability` entry**, update its
  row in `NOT-APPLICABLE.md` (change tag to `applicable-future` or
  `applicable`), open a new `phases/PHASE-NN-*.md` document with the actual
  port plan, and update `phases/README.md`.
- **Do not delete excluded entries** when scope changes. Mark them with a
  history note in `NOT-APPLICABLE.md` so the audit trail is preserved.
