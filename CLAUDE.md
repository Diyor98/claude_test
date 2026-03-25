# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

A browser-based game built with vanilla HTML/CSS/JS — no framework, no build tooling, no dependencies. Single `.html` file opened directly in a browser.

## Running

```bash
open mathflood.html
```

## Game: `mathflood.html`

Water rises from the bottom of the screen continuously. The player must solve math problems by typing answers into an input and pressing Enter. Correct answers push the water down; wrong answers make it jump up. Game over when water reaches the player character.

**Difficulty scales with time:**
| Time | Level | Operations | Numbers |
|------|-------|-----------|---------|
| 0–30s | Easy | `+`, `−` | 1–10 |
| 30–90s | Medium | `+`, `−`, `×`, `÷` | 1–20 |
| 90s+ | Hard | Two-op combos: `a + b×c`, `(a+b)×c`, `(a+b)÷c` | Mixed |

Division always produces integer answers (operands constructed from the answer outward).

**Key constants** (top of `<script>`):
- `WATER_RISE_BASE / WATER_RISE_MAX` — rise rate ramps from 0.8 to 3.5 %/s over 2 minutes
- `CORRECT_REWARD = 5` — % water drops on correct answer
- `WRONG_PENALTY = 3` — % water rises on wrong answer

**Water level** is driven by the CSS custom property `--water-height` set on `:root` each `requestAnimationFrame` tick. Both `#water` and `#player` use it via `calc()` with CSS transitions, so the JS only sets one value per frame.

**Game states**: `idle` → `playing` → `gameover` → `playing` (replay). The `void game.offsetHeight` reflow trick resets CSS animations on replay.

## Architecture & Conventions

When adding new games, follow this pattern:

- **Structure**: `<style>` block → HTML body → `<script>` block at the bottom
- **State**: plain `let` variables at the top of the script (no classes, no modules)
- **DOM**: cache all refs in a block after state variables; use `getElementById`
- **Animations**: CSS `@keyframes` + class toggling from JS; use `animationend` with `{ once: true }` to clean up classes
- **Game loop**: `requestAnimationFrame` with delta-time (`(timestamp - lastTick) / 1000`) for frame-rate-independent logic
- **CSS custom properties**: use `--variable` on `:root` to drive layout values that JS updates each frame

### Color Theme
- Background: `#1a1a2e`
- Card/surface: `#16213e`
- Border/dark: `#0f3460`
- Accent red: `#e94560`
- Accent cyan: `#a8dadc`
