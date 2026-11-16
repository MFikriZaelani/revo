# Implementation Plan: Cat Clicker

## Overview

Implement the entire Cat Clicker app as a single `index.html` file. The file contains all markup, Tailwind CDN config, custom styles, and Vanilla JavaScript modules (`AudioManager`, `AnimationController`, `ReactionSelector`, and the App bootstrap). No build step is required — the file opens directly in a browser.

## Tasks

- [x] 1. Scaffold `index.html` with page layout and Tailwind config
  - Create `index.html` at the workspace root with the full `<head>` block: Tailwind `config` object (custom `wiggle` and `zzz-fade` keyframes + their `animate-*` registrations), Tailwind CDN `<script>` tag, and Google Fonts `<link>` with a `sans-serif` fallback.
  - Add the full-viewport pastel gradient background (`#FFD1DC` → `#B5FFFC`) via inline `style` or Tailwind `bg-gradient-to-br` utilities.
  - Build the counter bar: two pill-shaped frosted-glass containers for `#pet-counter` and `#aura-counter`, always visible below the cat.
  - Create the cat container using `aspect-square` and Tailwind flexbox centering; add placeholder `#cat` SVG element, `#overlay-text`, `#zzz`, and `#speed-lines` DOM elements.
  - Add a `<style>` block defining the `zzz-fade` keyframe as a CSS fallback and any frosted-glass helper classes.
  - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5, 1.6, 2.1, 2.2, 2.3_

- [x] 2. Implement the SVG cat element and keyboard accessibility
  - Replace the placeholder with a complete inline SVG cat (120–200 px, drop shadow / platform shape beneath it, neutral resting state).
  - Add `tabindex="0"`, `role="button"`, and `aria-label` to `#cat` so it is reachable via Tab.
  - Wire `keydown` listener on `#cat` to fire the pet interaction on Enter or Space.
  - _Requirements: 2.1, 2.2, 2.3, 2.4_

- [x] 3. Implement `AudioManager`
  - [x] 3.1 Write the `AudioManager` IIFE module inside the `<script>` block
    - Instantiate `Audio()` objects for `pet.mp3`, `purr.mp3`, and `zoom.mp3` with inline comments listing each expected filename.
    - Implement `play(name, loop)`: set `loop`, call `.play()`, attach `.catch(() => {})`.
    - Implement `stop(name)`: call `.pause()`, reset `currentTime` to `0`.
    - _Requirements: 10.1, 10.2, 10.3, 10.4, 3.2, 3.5, 6.2, 6.3, 6.4, 8.4, 8.6_

  - [ ]* 3.2 Write property test for `AudioManager` — Property 7 and Property 8
    - Create `tests/audio-manager.test.js` using `fast-check` and a mock `Audio` class.
    - **Property 7: Audio replay always starts from the beginning** — for any sequence of 1–10 play/stop cycles, assert `currentTime === 0` immediately before each `play()` call that follows a `stop()`.
    - **Property 8: Audio play() rejection is always suppressed silently** — for each sound asset, assert no unhandled Promise rejection propagates when `play()` rejects.
    - **Validates: Requirements 10.4, 10.2, 3.5, 6.4, 8.6**

- [x] 4. Implement `ReactionSelector`
  - [x] 4.1 Write the `ReactionSelector` IIFE module
    - Implement `select(rng)` with the four range branches (1–40 happy, 41–70 purring, 71–90 sleeping, 91–100 zoomies).
    - Wrap the `rng()` call in a `try/catch`; default to `'happy'` for any exception, out-of-range value, `NaN`, `null`, or non-numeric return.
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 4.7_

  - [ ]* 4.2 Write property test for `ReactionSelector` — Property 2 and Property 4
    - Create `tests/reaction-selector.test.js` using `fast-check`.
    - **Property 2: Reaction selection is exhaustive and partition-complete** — for any integer in [1, 100], assert the return value is one of the four reaction strings and that each sub-range maps to the correct reaction.
    - **Property 4: Reaction selector falls back to Happy on invalid RNG output** — for RNG functions that throw, return `NaN`, `Infinity`, `0`, `101`, or `null`, assert return value is `'happy'`.
    - **Validates: Requirements 4.1, 4.2, 4.3, 4.4, 4.5, 4.7**

- [x] 5. Implement `AnimationController`
  - [x] 5.1 Write the `AnimationController` IIFE module
    - Define `REACTION_CONFIG` with per-reaction `catClasses`, `overlayText`, `showOverlay`, `showZzz`, `showSpeedLines`, `duration`, `auraGain`, `sound`, and `soundLoop` fields.
    - Implement `cancel()`: call `clearTimeout(activeTimer)` and run any pending `cleanupFn` synchronously.
    - Implement `trigger(reactionType, onAuraGain)`: call `cancel()` first, apply cat classes from config, show/hide `#overlay-text`, `#zzz`, and `#speed-lines` as appropriate, call `AudioManager` for reaction sounds, call `onAuraGain(config.auraGain)` for happy, then set `setTimeout` to remove all classes and hide overlays after `config.duration`.
    - _Requirements: 4.6, 5.1, 5.2, 5.4, 6.1, 6.2, 6.3, 7.1, 7.2, 7.4, 8.1, 8.3, 8.4, 8.5_

  - [ ]* 5.2 Write property test for `AnimationController` — Property 3 and Property 5
    - Create `tests/animation-controller.test.js` using `fast-check` and a fake DOM classList mock.
    - **Property 3: Aura score increases by exactly 5 on Happy, never changes on other reactions** — for any non-negative `auraScore` and any `reactionType`, assert `newAura === auraScore + (type === 'happy' ? 5 : 0)`.
    - **Property 5: AnimationController removes prior reaction classes before applying new ones** — for any two reaction types triggered in succession (second before first timeout), assert `catEl.classList` contains none of the first reaction's classes at the moment the second starts.
    - **Validates: Requirements 5.3, 4.6**

- [x] 6. Implement App bootstrap and counter formatting
  - [x] 6.1 Write the App bootstrap inside the `<script>` block
    - Declare `petCount = 0` and `auraScore = 0`.
    - Implement `formatPetCounter(n)` returning `"Total Pets: " + n + " 🐾"` and `formatAuraCounter(n)` returning `"Aura: " + n + " ✨"`.
    - Implement `updateCounterUI()` to set both counter elements' `textContent`.
    - Implement `handlePet()`: increment `petCount`, call `updateCounterUI()`, call `AudioManager.play('pet')`, call `ReactionSelector.select()`, call `AnimationController.trigger(reaction, delta => { auraScore += delta; updateCounterUI(); })`.
    - Wire `click` listener on `#cat` to `handlePet()`; initialize counters on page load.
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.6, 5.3, 5.5_

  - [ ]* 6.2 Write property test for counter formatting — Property 1 and Property 6
    - Create `tests/app-bootstrap.test.js` using `fast-check`.
    - **Property 1: Pet counter always increments by exactly 1** — for any integer `petCount` in [0, 999999998], assert `handlePet` produces `petCount + 1`.
    - **Property 6: Counter display format invariant** — for any integer in [0, 999999999], assert `formatPetCounter(n) === "Total Pets: " + n + " 🐾"` and `formatAuraCounter(n) === "Aura: " + n + " ✨"`.
    - **Validates: Requirements 3.1, 3.3, 5.5**

- [x] 7. Checkpoint — wire all modules together and verify the full interaction loop
  - Confirm all four modules (`AudioManager`, `AnimationController`, `ReactionSelector`, App bootstrap) are present in the `<script>` block in the correct dependency order.
  - Open `index.html` directly in a browser; click the cat at least 20 times and confirm: pet counter increments, aura counter increments on happy reactions, all four reaction animations run, overlay/zzz/speed-line elements appear and disappear, no JS console errors occur.
  - Ensure all tests pass, ask the user if questions arise.

- [x] 8. Implement custom Tailwind animation configuration
  - Verify the `tailwind.config` object (placed before the CDN `<script>`) defines `wiggle` keyframe (`translateX(-10px)` ↔ `translateX(10px)`), registers `animate-wiggle` at `0.1s infinite`, defines `zzz-fade` keyframe (opacity 0 → 1 at 50% → 0 at 100%), and registers `animate-zzz` at `2s forwards`.
  - Confirm `animate-bounce` and `animate-pulse` are available from Tailwind core (no custom definition needed).
  - _Requirements: 9.1, 9.2, 9.3, 9.4, 9.5_

- [x] 9. Final checkpoint — full regression and polish
  - Run all property-based and unit tests: `node --experimental-vm-modules node_modules/.bin/jest tests/` (or equivalent).
  - Confirm `index.html` renders correctly when opened from the filesystem (`file://` protocol) with no server.
  - Confirm missing audio files do not throw unhandled errors.
  - Confirm keyboard navigation (Tab to focus cat, Enter/Space to pet) works end-to-end.
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for a faster MVP.
- All implementation lives in a single `index.html` file; test files live in a `tests/` subdirectory alongside it.
- Property tests use [fast-check](https://github.com/dubzzz/fast-check) with a minimum of 100 iterations per property.
- Checkpoints validate the full interaction loop incrementally rather than waiting until the end.
- Audio assets (`pet.mp3`, `purr.mp3`, `zoom.mp3`) must be placed alongside `index.html`; the app degrades gracefully when they are absent.

## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["3.1", "4.1"] },
    { "id": 1, "tasks": ["3.2", "4.2", "5.1"] },
    { "id": 2, "tasks": ["5.2", "6.1"] },
    { "id": 3, "tasks": ["6.2"] }
  ]
}
```
