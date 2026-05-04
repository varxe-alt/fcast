# Phase 16 — Bitrate & Quality Presets Placeholder

> Settings sub-page for bitrate presets, with a quick-action shortcut.
> **UI-only.** Presets are stored as inline mock model; no Rust encoder
> reconfiguration.

**Status:** `[ ] Not started`
**Depends on:** Phases 2, 3, 7
**Functional integration:** Deferred — encoder bitrate change requires
GStreamer `videorate` + `videoconvert` + `x264enc` (or `amcvidenc`) bitrate
property updates.
**Moblin source analogues:**
- `Settings/BitratePresets/BitratePresetsSettingsView.swift`
- `Settings/BitratePresets/BitratePresetsPresetSettingsView.swift`
- `ControlBar/QuickButton/QuickButtonBitrateView.swift`

**Files:**
- `senders/android/ui/pages/bitrate_presets_page.slint` — new
- `senders/android/ui/pages/bitrate_preset_edit_page.slint` — new (single-preset editor)
- `senders/android/ui/bridge.slint` — `Panel.bitrate-presets`, `Panel.bitrate-preset-edit`

---

## Tasks

### 16-A — `BitratePreset` struct

- [ ] In `bridge.slint`, add the struct (placeholder — not bound to any
  `Bridge` setter):

  ```slint
  export struct BitratePreset {
      id:       string,
      name:     string,
      bitrate-kbps: int,
      active:   bool,
  }
  ```

---

### 16-B — `BitratePresetsPage` (list)

- [ ] Page-level inline mock model:

  ```slint
  in-out property <[BitratePreset]> mock-presets: [
      { id: "low",    name: "Low",      bitrate-kbps: 1500,  active: false },
      { id: "med",    name: "Medium",   bitrate-kbps: 4000,  active: true  },
      { id: "high",   name: "High",     bitrate-kbps: 8000,  active: false },
      { id: "max",    name: "Maximum",  bitrate-kbps: 15000, active: false },
  ];
  in-out property <int> mock-selected-idx: 1;
  ```

- [ ] Render each preset as a `Rectangle` row showing name + bitrate, with a
  trailing chevron. Tapping a row sets `mock-selected-idx` and updates each
  preset's `active` flag (UI-only — the `for` loop re-renders).
- [ ] Add an "Add preset" `PrimaryButton` that opens
  `Bridge.active-panel = Panel.bitrate-preset-edit;` (with no real persistence).

---

### 16-C — `BitratePresetEditPage` (single preset)

- [ ] Form with name `LineEdit` + bitrate `SettingsSliderRow` (range
  500..20000 kbps step 500) + Save button.
- [ ] Save button does `Bridge.active-panel = Panel.bitrate-presets;` (no real
  mutation of the parent's `mock-presets`).
- [ ] Note in the doc: real persistence requires lifting the model to a
  global / `Bridge`, deferred to `futures/`.

---

### 16-D — Quick-action shortcut

- [ ] Add a `quick-action` entry in the control-bar stub model with id
  `"bitrate"`. On click in Slint, jump to `Panel.bitrate-presets`.

---

## Exit criteria

1. `BitratePresetsPage` opens from settings root, lists 4 stub presets, shows
   active checkmark on Medium.
2. Tapping a preset highlights it (active flag flips locally).
3. `BitratePresetEditPage` opens from "Add preset" — slider drags update label.
4. Quick-action "bitrate" opens the list page directly.
5. `cargo build -p android-sender` passes.

---

## What's NOT in this phase

- Persisting preset edits (requires Rust storage).
- Live encoder bitrate reconfiguration.
- "Network-aware" auto-bitrate (Moblin-specific feature; deferred indefinitely).
- Per-preset codec / profile / framerate overrides.

---

## Slint best practices applied here

- **A `for` loop over `mock-presets` re-renders reactively** when the list is
  mutated. Updating `active` flags within the same `in-out` property
  propagates without manual notification.
- **Two-page panel pattern (list + edit)** mirrors Moblin's nav stack but uses
  the simpler `Panel` enum routing instead of a stack.
