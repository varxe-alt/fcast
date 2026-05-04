# Phase 27 — Reusable Utils Backlog Placeholder

> Sweep up the remaining Moblin `View/Utils/*.swift` files as tiny reusable
> Slint components, only when a consuming phase actually needs them.
> **UI-only — utility components have no functional behaviour to wire.**

**Status:** `[ ] Ongoing — pull from this list when a downstream phase needs a util`
**Depends on:** Phase 3 (already covers the most common utils)
**Functional integration:** N/A (these are pure UI primitives).
**Moblin source analogues:** `View/Utils/*.swift` (remaining un-ported files
after Phase 3).

**Files:**
- `senders/android/ui/components/text_edit.slint` — wraps `LineEdit` with label + error text
- `senders/android/ui/components/multi_line_text_field.slint` — multi-line edit
- `senders/android/ui/components/value_edit.slint` — numeric edit with +/- chips
- `senders/android/ui/components/inline_picker.slint` — ComboBox-style inline picker
- `senders/android/ui/components/icon_and_text.slint` — small icon + text row
- `senders/android/ui/components/info_banner.slint` — transient banner pill
- `senders/android/ui/components/form_field_error.slint` — small red error text
- `senders/android/ui/components/draggable_item_prefix.slint` — drag-handle glyph
- `senders/android/ui/components/urls_view.slint` — list of clickable-looking URLs

---

## Goal

Provide a backlog of small reusable components that map 1:1 to Moblin's
`View/Utils/*.swift` files. Each component is tiny (< 80 lines) and
implemented **only when a downstream phase needs it**, not speculatively.

---

## Component sketches

> These are reference sketches. Build the component on-demand when a phase
> needs it — do not implement all of them at once.

### `TextEditField`
```slint
import { LineEdit } from "std-widgets.slint";
import { Theme } from "../theme.slint";
import { FormFieldError } from "form_field_error.slint";

export component TextEditField inherits VerticalLayout {
    in-out property <string> text;
    in property <string> label;
    in property <string> placeholder;
    in property <string> error: "";
    spacing: 4px;
    Text { text: root.label; color: Theme.text-secondary; font-size: Theme.font-size-label; }
    LineEdit {
        text <=> root.text;
        placeholder-text: root.placeholder;
    }
    if root.error != "": FormFieldError { message: root.error; }
}
```

### `MultiLineTextField`
- `TextEdit` from std-widgets wrapped with label + max-line height.

### `ValueEditChip`
- Compact numeric editor: `[ - ] 42 [ + ]`. Three nested elements; clicks
  mutate a bound int.

### `InlinePicker`
- Wrap `ComboBox` from std-widgets with a `label` + leading icon.

### `IconAndText`
- Tiny `HorizontalLayout { spacing: 6px; Image; Text }` — frequently used in
  context-menu items and settings rows.

### `InfoBanner`
- Pill-shaped banner: bg `Theme.surface-overlay`, fg `Theme.text-primary`,
  optional severity tint. Used by Phases 19 (export success), 22 (Wi-Fi Aware
  placeholder warning).

### `FormFieldError`
- Single-line `Text { color: Theme.error; font-size: Theme.font-size-label; }`.

### `DraggableItemPrefix`
- A 6-dot glyph rendered as 6 small `Rectangle`s. Visual hint that a row is
  draggable (real drag is deferred; see Phase 17's note).

### `UrlsView`
- A model-driven list of `TextButton`s with link styling. Click is a no-op in
  UI-only build (real intent launch deferred).

---

## When to add each

| Component | Triggered by phase |
|---|---|
| `TextEditField` | Phase 24 (rename), Phase 25 (macro name), Phase 6-E (manual IP) |
| `MultiLineTextField` | Phase 25 (macro description, future) |
| `ValueEditChip` | Phase 16 (bitrate edit), Phase 18 (snapshot countdown seconds) |
| `InlinePicker` | Phase 14 (audio source), Phase 15 (camera source) |
| `IconAndText` | Phase 13 (status badges), Phase 20 (history rows) |
| `InfoBanner` | Phase 19 (backup success), Phase 22 (Wi-Fi Aware placeholder) |
| `FormFieldError` | Any form (Phase 24 rename, Phase 25 macro edit) |
| `DraggableItemPrefix` | Phase 17 (quick-action reorder), Phase 25 (macro step reorder) |
| `UrlsView` | Phase 21 (help links) |

---

## Exit criteria (per component)

A component is "done" when:

1. It exists as a separate `.slint` file under `senders/android/ui/components/`.
2. It compiles in isolation (`slint-viewer` opens it standalone).
3. At least one downstream phase imports it.
4. Theme tokens are used throughout (no raw hex; matches Phase 2's audit).
5. `cargo build -p android-sender` passes.

---

## What's NOT in this phase

- Speculative implementation of components no phase needs yet.
- Direct ports of Moblin utility *behaviours* (e.g. `SwipeLeftTo*`,
  `StrokeModifier`, `HCenter`) — these are SwiftUI mechanism wrappers that
  Slint expresses natively (gestures, `border-width`, `alignment: center`).

---

## Slint best practices applied here

- **Tiny single-purpose components > monolithic util libraries.** Each
  component lives in its own `.slint` file and is self-contained.
- **Build on demand.** Resist the urge to port all of Moblin's `Utils/*`
  speculatively — most are SwiftUI-specific patterns that Slint doesn't
  need (e.g. `HCenter`, `StrokeModifier`).
- **Theme tokens everywhere.** Each new util MUST go through Phase 2's
  audit greps — zero hex literals, zero raw `steelblue`.
