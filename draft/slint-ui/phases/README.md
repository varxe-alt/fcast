# FCast Slint UI — Phase Files

Each file in this directory tracks one phase of the Slint UI evolution plan.
Start with `PHASE-0` and work forward. See `../TODO.md` for the original
high-level overview, and `../futures/NOT-APPLICABLE.md` for per-Moblin-file
applicability triage.

## Roadmap shape

The roadmap is split into two bands:

- **Phases 0–11 (foundation):** audit, module split, theme, reusable
  components, control bar, status overlay, receiver list, settings root,
  Rust bridge gate, localization, validation, source tracking. Phases 5–7
  and 9–11 are **UI-only** placeholder specs (no Rust wiring); Phase 8
  is explicitly **deferred** until the UI is sealed.
- **Phases 12–27 (feature placeholders):** one phase per cluster of
  `applicable-future` / `deferred` features from
  `../futures/NOT-APPLICABLE.md`. Every phase ships **UI without
  functionality** — controls flip inline `in-out` properties, no Bridge
  setters, no Rust callbacks.

## Phase index

| File | Phase | One-line summary | Depends on |
|---|---|---|---|
| `PHASE-0-baseline-audit.md` | 0 | Confirm Slint version, build, Bridge contract, codec status | — |
| `PHASE-1-split-modules.md` | 1 | Split `main.slint` into `bridge/theme/pages/components` files | 0 |
| `PHASE-2-theme-tokens.md` | 2 | Replace all hardcoded colors/sizes with `Theme` tokens | 1 |
| `PHASE-3-components.md` | 3 | Build `PrimaryButton`, `SettingsValueRow`, etc. | 1, 2 |
| `PHASE-4-control-bar.md` | 4 | `CastControlBar` + model-driven `QuickAction` buttons | 1, 2, 3 |
| `PHASE-5-status-overlay.md` | 5 | `StatusOverlay` pills on casting screen (UI-only) | 1, 2, 4 |
| `PHASE-6-receiver-list.md` | 6 | `ReceiverItem` rows + spinner empty state (UI-only) | 1, 2, 3 |
| `PHASE-7-settings-pages.md` | 7 | `FullSettingsPage` + `Panel` routing (UI-only) | 1, 2, 3, 4 |
| `PHASE-8-rust-bridge.md` | 8 | **Deferred** — Rust wiring placeholder, parked in `futures/` | 5, 6, 7 + 12–27 |
| `PHASE-9-localization.md` | 9 | Wrap all strings with `@tr("...")`, generate `.pot` template | 7, 12–27 |
| `PHASE-10-testing.md` | 10 | Build gate + visual validation checklist (runs each phase) | each phase |
| `PHASE-11-source-tracking.md` | 11 | Moblin → Slint group completeness reference table | reference |
| `PHASE-12-capture-preview.md` | 12 | Capture preview placeholder card on casting screen | 1, 2, 5 |
| `PHASE-13-status-badges-row.md` | 13 | Battery / thermal / network badge row above control bar | 1, 2, 3, 4 |
| `PHASE-14-audio-capture-controls.md` | 14 | Audio settings sub-page (source / mute / gain / bitrate) | 2, 3, 7 |
| `PHASE-15-camera-capture-controls.md` | 15 | Camera settings sub-page (source / res / framerate / mirror / zoom) | 2, 3, 7 |
| `PHASE-16-bitrate-quality-presets.md` | 16 | Bitrate presets list + per-preset editor | 2, 3, 7 |
| `PHASE-17-quick-action-customization.md` | 17 | Quick-action reorder / enable / overflow banner | 2, 3, 4, 7 |
| `PHASE-18-privacy-lifecycle-modes.md` | 18 | Lock / Stealth / Snapshot Countdown overlays | 1, 2, 3 |
| `PHASE-19-settings-backup-reset.md` | 19 | Backup / import / reset destructive flows + `ConfirmDialog` | 2, 3, 7 |
| `PHASE-20-cast-history.md` | 20 | Cast session history list + per-session detail | 2, 3, 7, 19 |
| `PHASE-21-help-and-support.md` | 21 | About / version history / attributions / help | 2, 3, 7 |
| `PHASE-22-network-interface-wifi-aware.md` | 22 | Network interface list + Wi-Fi Aware opt-in placeholder | 2, 3, 7 |
| `PHASE-23-local-recording.md` | 23 | Recording controls placeholder (idle / recording / paused) | 2, 3, 7 |
| `PHASE-24-pairing-qr-receiver-management.md` | 24 | QR pairing page + receiver context menu (rename / forget / default) | 2, 3, 6 |
| `PHASE-25-macros-action-chains.md` | 25 | Macros list + per-macro step editor | 2, 3, 7, 17 |
| `PHASE-26-debug-log-viewer.md` | 26 | Debug log viewer + video pipeline state | 2, 3, 7 |
| `PHASE-27-utils-backlog.md` | 27 | Reusable utility components, built on demand | 3 |
| `APPENDIX-blockers-and-decisions.md` | — | Codec blockers, arch decisions, dependency graph, risks | — |

## Quick-start order

```
Phase 0  →  Phase 1  →  Phase 2  →  Phase 3
                                         ├──→  Phase 4  →  Phase 5  →  Phases 12, 13
                                         ├──→  Phase 6  →  Phases 24
                                         └──→  Phase 7  →  Phases 14, 15, 16, 17, 18,
                                                          19, 20, 21, 22, 23, 25, 26
Phase 27 is on-demand: pull when a downstream phase needs a util.
Phase 8 stays deferred until UI sign-off.
Phase 9 sweeps any UI phase's strings into @tr() after merge.
Phase 10 runs alongside every UI phase.
Phase 11 is a living reference, updated as phases complete.
```

## UI-only discipline

Phases 5–7 and 12–27 are **UI without functionality**:

- All stub data lives as inline `in-out property <[T]> mock-...` on the
  page component.
- Controls read/write the stub properties — no Rust callbacks, no Bridge
  setters.
- Audit greps in Phase 8 catch regressions
  (`Bridge.(status-items|quick-actions|app-version)` must not appear in UI
  files until that phase is reactivated).

This lets the UI evolve independently of Rust, ship as a previewable APK,
and absorb design feedback before any wiring work begins.

## Status key

Each phase file has a `**Status:**` line at the top:

| Status | Meaning |
|---|---|
| `[ ] Not started` | No work begun |
| `[ ] In progress` | Some tasks checked off |
| `[x] Complete` | All exit criteria met |
| `[ ] Blocked` | Waiting on a dependency (codec or decision) |
| `[ ] Deferred` | Intentionally parked (Phase 8 — Rust wiring; promoted later) |
| `Defer` | Intentionally excluded from current scope |

## What changed vs. the original `TODO.md`

The original `draft/slint-ui/TODO.md` (Phases 0–9) was speculative and
did not reference actual codebase paths. These phase files are grounded in
the real project:

- Phase 0 contains the actual `Bridge` contract table extracted from `main.slint`.
- Phases 1–3 include exact file paths and build-check steps.
- Phases 4–7 include concrete Slint code snippets.
- Phases 12–27 ship **UI without functionality**, each tied to a specific
  Moblin source group from `../futures/NOT-APPLICABLE.md`.
- Phase 8 is reframed as a deferred Rust-wiring placeholder.
- The Appendix captures the dependency graph and codec blockers from `TODO.codecs.md`.
