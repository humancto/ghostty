# Ghostty Roadmap Proposals (Draft)

> **Status:** Draft for review on a personal fork.
> **Authorship:** AI-assisted (Claude). Not a maintainer proposal. Not for
> upstream submission in this form. If anything here ships upstream, it must
> first be rewritten and owned by a human contributor per the project's
> [AI Usage Policy](AI_POLICY.md).

This document collects 24 small, low-risk, measurable proposals that aim to
amplify Ghostty's existing strengths (a best-in-class VT engine, an unused
shadertoy renderer, a partial Command Palette, Sentry-format crash reports)
into shippable artifacts that double as marketing assets.

Each item is sized for **days, not weeks**, avoids the parser/renderer hot
paths, and is gated by a single config flag or CLI subcommand so the blast
radius is contained.

## How to read each item

- **Scope:** rough effort estimate
- **Risk:** what could break
- **Metric:** the number or artifact this produces
- **Why it travels:** the specific virality mechanic, not generic appeal
- **Touches:** the paths most likely to change

---

## A. Measurement & Marketing

These produce a number, a graph, or a public artifact. The marketing value is
that the number exists at all — most terminals do not publish theirs.

### A1. `ghostty +bench-latency`

- **Scope:** ~300 LOC CLI subcommand.
- **Risk:** Zero. Read-only, off the hot path.
- **Metric:** key-to-photon P50/P95/P99 in ms.
- **Why it travels:** "We publish our latency. Run it on your machine." That
  sentence is rare in the terminal market. A single reproducible number is
  unfakeable and frequently quoted.
- **Touches:** `src/cli/`, reads frame timestamps already in
  `src/renderer/Thread.zig`.

### A2. Public per-commit perf dashboard

- **Scope:** ~1 day after A1 exists.
- **Risk:** Zero. CI artifact only.
- **Metric:** Time-series graph of A1's numbers per commit on `main`.
- **Why it travels:** the graph itself is the asset. Every regression has a
  date, a commit, a blame. People link to graphs.
- **Touches:** `.github/workflows/`, GitHub Pages.

### A3. Generated JSON Schema for `ghostty.conf`

- **Scope:** ~150 LOC generator + CI step.
- **Risk:** Additive. No runtime change.
- **Metric:** A `ghostty.conf.schema.json` published per release.
- **Why it travels:** opening `ghostty.conf` in VS Code, Helix, or Zed gives
  autocomplete and validation with zero plugin install. People screenshot
  this and post it.
- **Touches:** `src/config/Config.zig` already has the field metadata; emit
  during `zig build`.

### A4. `ghostty +doctor`

- **Scope:** ~250 LOC.
- **Risk:** Zero. Pure read.
- **Metric:** A diagnostic report (shell-integration, font cache, GPU backend,
  terminfo, locale, conflicting envs, fractional scaling state).
- **Why it travels:** small UX moment that ends up in "things I love about X"
  posts. Operationally, it lets every bug report start with "paste your
  `+doctor` output," cutting triage cost.
- **Touches:** `src/cli/`.

### A5. `ghostty +crash-report show <path>`

- **Scope:** ~200 LOC. README explicitly admits this is missing.
- **Risk:** Zero. File reader.
- **Metric:** Pretty-printed crash report from a Sentry envelope.
- **Why it travels:** closes a documented promise; a small but visible signal
  that the project follows through.
- **Touches:** `src/cli/`, `src/crash/`.

### A6. Public conformance matrix

- **Scope:** 1–2 days. The corpus already exists in `src/terminal/`.
- **Risk:** Zero.
- **Metric:** A machine-readable `dist/conformance.json` per release listing
  pass/fail per ECMA-48, VT220, and xterm sequence.
- **Why it travels:** a single sentence — *"Ghostty passes N of M sequences"* —
  with a link is a defensible claim. Other terminals will be compared to it.
- **Touches:** `src/terminal/` test harness, `build/`.

### A7. Reproducible benchmark Nix flake

- **Scope:** Half a day.
- **Risk:** Zero.
- **Metric:** A `nix run github:.../ghostty#bench` (or `make bench`) one-liner
  that runs vtebench against Ghostty + alacritty/kitty/foot from nixpkgs and
  prints a comparison table.
- **Why it travels:** disarms "you cherry-picked numbers" criticism. Anyone
  can repro on their own hardware in one command.
- **Touches:** `flake.nix`, `Makefile`.

### A8. Memory-per-pane published metric

- **Scope:** ~150 LOC.
- **Risk:** Zero.
- **Metric:** `ghostty +stats` showing RSS/PSS, `PageList` pages, atlas memory,
  surface count.
- **Why it travels:** a defensible single-digit-MB-per-pane number is
  quotable. Memory regressions become trivial to spot.
- **Touches:** `src/cli/`, reads from existing structures.

### A9. Cold-start budget enforced in CI

- **Scope:** Half a day.
- **Risk:** Zero (CI only).
- **Metric:** Time to first frame from `ghostty -e true`. Budget (e.g. 80ms on
  the runner). PRs over budget fail.
- **Why it travels:** "fastest cold start" becomes a concrete, testable claim.
- **Touches:** `.github/workflows/`.

### A10. `ghostty +list-themes --json --preview`

- **Scope:** ~50 LOC over what exists.
- **Risk:** Zero.
- **Metric:** Machine-readable theme list, `--preview` prints a 4-line color
  sample.
- **Why it travels:** unblocks a website theme gallery; scriptable for editor
  plugins; community-shareable.
- **Touches:** `src/cli/`.

---

## B. Visual flair

These produce screenshots or short videos. They are the part of the project
people screenshot and post unprompted.

### B1. Curated first-party shader gallery

- **Scope:** Mostly artistic. The `shadertoy.zig` infra and uniforms (cursor
  color, focus, palette, cursor change time) already exist; the test fixtures
  `test_shadertoy_crt.glsl` and `test_shadertoy_focus.glsl` already run.
- **Risk:** Zero. Opt-in assets.
- **Metric:** 6–8 polished shaders ship under `~/.config/ghostty/shaders/`
  (subtle CRT, modern scanline, focus glow, cursor ink bleed, vignette,
  amber, bloom-on-bright).
- **Why it travels:** every screenshot of Ghostty becomes a recruiting poster.
  A single 10-second video toggling between them lands on Hacker News.
- **Touches:** `src/renderer/shaders/`, config docs.

### B2. Frame-timing HUD overlay

- **Scope:** Small. `Overlay.zig` exists; frame timing is already in
  `Thread.zig`.
- **Risk:** Zero. Off by default, toggled by keybind.
- **Metric:** FPS, frame-time histogram, GPU vs CPU split, dirty-cell %.
- **Why it travels:** lands on r/unixporn. Also doubles as the live proof for
  A1/A2.
- **Touches:** `src/renderer/Overlay.zig`.

### B3. Live theme browser with keystroke preview

- **Scope:** Small.
- **Risk:** Zero. Uses existing theme code path.
- **Metric:** `ghostty +themes` interactive picker; arrow keys preview live
  on a sample pane, Enter saves.
- **Why it travels:** "wait, you can do *that* in a terminal?" energy.
  Excellent in a 5-second screen recording.
- **Touches:** `src/cli/`, theme loading.

### B4. First-run welcome (opt-out)

- **Scope:** Small.
- **Risk:** Zero. One-shot, opt-out.
- **Metric:** ASCII welcome with version, GPU backend, and pointers to
  `+doctor`, command palette, theme picker.
- **Why it travels:** every first-time user posts a screenshot of a polished
  first launch. Free first-impression marketing.
- **Touches:** `src/Surface.zig` startup path, gated by a one-shot state file.

### B5. Cursor ink trail / motion smoothing

- **Scope:** Small. `previous_cursor` and `current_cursor` uniforms already
  exist.
- **Risk:** Purely visual, off by default.
- **Metric:** Configurable cursor interpolation, ~20ms ease.
- **Why it travels:** Neovide rode this single feature into hundreds of
  YouTube videos. Instantly recognizable visual signature.
- **Touches:** `src/renderer/cursor.zig`, shader uniforms.

### B6. Animated dark/light palette transition

- **Scope:** Small. Ghostty already gets the OSC light/dark notification.
- **Risk:** Zero. A tween over a small color array.
- **Metric:** ~200ms crossfade instead of an instant snap.
- **Why it travels:** people record OS theme switches; this becomes the
  "Ghostty did *what*?" moment in those videos.
- **Touches:** palette application path.

---

## C. Operational coolness

These are power-user features that get talked about in dotfile communities
and developer blogs.

### C1. Native `ghostty record` / `ghostty replay`

- **Scope:** Medium-small. PTY byte stream + window size + timing into an
  asciinema-v2-compatible JSON; `+replay` plays back inside a real Ghostty
  pane. Optional `--gif` and `--svg` exporters use the existing renderer.
- **Risk:** Low. Read/write at the existing PTY boundary.
- **Metric:** A `.cast` file produced and replayable.
- **Why it travels:** every blog post about Ghostty needs a demo gif. Ship
  the recorder and Ghostty becomes the de-facto recording tool. Bug reports
  start coming with reproductions instead of screenshots.
- **Touches:** `src/cli/`, `src/pty.zig`, `src/termio/`.

### C2. Cross-platform Command Palette

- **Scope:** Medium. macOS already has `CommandPalette.swift` and
  `TerminalCommandPalette.swift`. Linux/GTK has nothing equivalent.
- **Risk:** Moderate but contained. Apprt-only, no core change.
- **Metric:** Cmd/Ctrl-Shift-P → fuzzy search every keybinding, action, theme,
  recent CWD on Linux too.
- **Why it travels:** the modern editor feature people miss most when they
  leave VS Code/Zed for a terminal.
- **Touches:** `src/apprt/gtk/`.

### C3. Pane "energy mode"

- **Scope:** Small. Ghostty's render is already event-driven.
- **Risk:** Low. Behind a config flag with hysteresis to avoid flicker.
- **Metric:** Inactive idle panes drop to 1Hz and skip GPU upload; publish
  `powermetrics` numbers showing 0% GPU at idle.
- **Why it travels:** "the terminal that doesn't drain your battery" is a
  defensible claim Apple-laptop users repeat unprompted.
- **Touches:** `src/renderer/Thread.zig`, `src/renderer/State.zig`.

### C4. Tab thumbnail on hover

- **Scope:** Small. Renderer already produces per-surface frames.
- **Risk:** Low. Off-screen render only on hover.
- **Metric:** Hovering a tab shows a small live thumbnail of recent output.
- **Why it travels:** a visible "Ghostty does things other terminals don't"
  moment. Good in screenshots.
- **Touches:** apprt tab UIs, an offscreen render target.

### C5. `ghostty +export` / `+import` config bundles

- **Scope:** Small.
- **Risk:** Low. `+import` always shows a confirmation dialog listing every
  change.
- **Metric:** A signed `.ghostty-pack` containing config + custom shaders +
  theme. `ghostty +import url-or-path` applies it after explicit confirm.
- **Why it travels:** spawns a community ecosystem of "rice packs." Every
  shared dotfile post becomes a one-click import. Moves Ghostty into the
  rice-culture zeitgeist.
- **Touches:** `src/cli/`, config loader.

### C6. Smart paste preview overlay

- **Scope:** Small. Risky-paste warning already exists.
- **Risk:** Low. Visual layer only.
- **Metric:** Multi-line pastes show first/last lines, total line count,
  confirm/cancel, in a polished overlay.
- **Why it travels:** the kind of small detail that signals "this app cares"
  and gets quoted in reviews.
- **Touches:** `src/Surface.zig`, apprt overlay.

### C7. URL hover-preview card

- **Scope:** Small without title fetch; medium with opt-in title fetch.
- **Risk:** Low. Title fetch is opt-in and uses the OS proxy stack.
- **Metric:** Hover a detected URL → small card with hostname, scheme,
  optional fetched `<title>`.
- **Why it travels:** browser-class polish in a terminal. Most terminals do
  not even validate the link.
- **Touches:** `src/renderer/link.zig`, apprt overlay.

### C8. `ghostty://` deep link scheme

- **Scope:** Small on macOS (apprt URL handler exists); medium on Linux.
- **Risk:** Low. Gated behind explicit permission prompt on first use.
- **Metric:** `ghostty://run?cmd=...` URLs run a command in a new window
  after a confirmation prompt.
- **Why it travels:** a primitive nobody else has. Documentation sites,
  tutorials, and `gh` repos can have one-click "open this command in your
  terminal" buttons. Strong viral mechanic for tutorials.
- **Touches:** `src/apprt/`, OS URL handlers.

---

## Suggested first wave (two weeks)

If only one batch ships, the highest leverage-per-day combination is:

1. **A1** + **A9** — two published numerical claims.
2. **A2** — turns those numbers into a public regression dashboard.
3. **A3** — visible UX win, picked up by editors with no install.
4. **A4** — cuts support cost, generates goodwill.
5. **A7** + **A5** — close two open gaps cheaply.

End state after two weeks: three public numerical claims with a regression
dashboard, an editor-integrated config, a self-diagnose command, a
reproducible benchmark anyone can run, and a working crash-report viewer.
None of it touched the parser, renderer, or terminal state machine.

## Suggested second wave (weeks 3–4)

The cool factor wave:

1. **B1** — shader gallery (mostly artistic effort).
2. **C1** — record/replay. Compounds across every future demo and bug report.
3. **B5** — cursor ink trail. Single most recognizable visual signature.

## What is deliberately *not* on this list

- Plugin / scripting surface (Lua, Wasm). Worth doing, but it is not small,
  not low-risk, and not measurable on a dashboard. Belongs in a separate
  proposal with a real design.
- Wayland-native and KDE/Qt apprts. Same reason — large, multi-month, and
  needs platform expertise.
- Ghostty-only control sequences (README roadmap step 6). Inherently a
  cross-vendor specification effort, not a shippable item.
- Windows native apprt. Major undertaking, not a small win.

These are real opportunities but they belong to a different kind of plan.

---

## Disclosure

This file was drafted with AI assistance (Claude) on a personal fork for
review. It does not represent the views of the Ghostty maintainers and
should not be treated as a maintainer-endorsed roadmap. Any subset that
moves toward upstream must first be rewritten and owned by a human
contributor per [`AI_POLICY.md`](AI_POLICY.md).
