# Phase 12 — Capture Preview Placeholder

> Add a "what's being cast" preview surface to the casting screen. **UI-only —
> no real frame data.** The preview shows a static placeholder image / pattern
> until Rust exposes the captured frame source.

**Status:** `[ ] Not started`
**Depends on:** Phases 1, 2, 5 (status overlay co-locates here)
**Functional integration:** Deferred — no MediaProjection / surface texture wiring.
**Moblin source analogues:** `View/Stream/StreamView.swift`
**Files:**
- `senders/android/ui/components/capture_preview.slint` — new
- `senders/android/ui/pages/casting_page.slint` — embed preview behind status overlay

---

## Goal

Show a placeholder rectangle on the casting page representing the captured
screen/audio source. Real GStreamer frame data is parked in `futures/`.

---

## Tasks

### 12-A — Component `CapturePreview`

- [ ] Create `senders/android/ui/components/capture_preview.slint`.
- [ ] Implement:

  ```slint
  import { Theme } from "../theme.slint";

  export component CapturePreview inherits Rectangle {
      // UI-only stub: when image data is wired, replace `mock-source-label`
      // with a real `image-source: image` property bound to Bridge.
      in property <string> mock-source-label: "Screen capture";
      in property <bool>   mock-active: true;

      background: Theme.surface-card;
      border-radius: Theme.radius-card;
      clip: true;

      // Diagonal stripe pattern as a stand-in for "live preview"
      Rectangle {
          background: root.mock-active ? Theme.accent-pressed : Theme.surface-overlay;
          opacity: 0.15;
      }

      VerticalLayout {
          alignment: center;
          spacing: 6px;
          Text {
              text: root.mock-active ? "● LIVE" : "○ Idle";
              color: root.mock-active ? Theme.error : Theme.text-secondary;
              font-size: Theme.font-size-label;
              horizontal-alignment: center;
          }
          Text {
              text: root.mock-source-label;
              color: Theme.text-primary;
              font-size: Theme.font-size-body;
              horizontal-alignment: center;
          }
      }
  }
  ```

- [ ] **Build check.**

---

### 12-B — Embed in `CastingView`

- [ ] In `casting_page.slint`, add `CapturePreview` between the page padding
  and the existing "Casting" title:

  ```slint
  CapturePreview {
      width: parent.width - Theme.padding-screen * 2;
      height: 200px;
      mock-source-label: "Screen capture (1920×1080)";
      mock-active: true;
  }
  ```

- [ ] Status overlay (Phase 5) should still render on top — verify z-order.
- [ ] **Build check.**

---

## Exit criteria

1. `CapturePreview` renders a card with "● LIVE" + label.
2. Toggling `mock-active: false` shows "○ Idle" with muted background.
3. Status overlay continues to render above the preview.
4. `cargo build -p android-sender` passes.

---

## What's NOT in this phase

- Real MediaProjection / Slint `Image` source bound to GStreamer output.
- Audio waveform / level meter.
- Picture-in-picture mode.
- Tap-to-pause-preview gesture.

---

## Slint best practices applied here

- **Plain `Rectangle` + nested `VerticalLayout`** is the cheapest possible
  preview placeholder — no `Image` element until we have real bytes.
- **`clip: true`** on the outer rectangle prevents future texture content
  from bleeding past the rounded corners.
