# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the App

No build step — open `index.html` directly in a browser. There is no package.json, bundler, test framework, or linter.

## Architecture

The entire application is a single file: `index.html` (~1,500 lines).

**File layout:**
- Lines 1–401: CSS (CSS variables, screen layout, animations)
- Lines 402–570: HTML (7 screens + scratchpad modal, all stacked via `.screen`/`.screen.active`)
- Lines 571–1495: JavaScript

**Screens (shown/hidden by toggling `.active`):**
`#home` → `#avatar-select` → `#battle` → `#victory` / `#defeat` → `#win` → `#arcade`

**Key JS sections:**
- `AVATARS`, `MONSTERS`, `MODES` constants — static game data
- `G` object — ephemeral battle state (hp, score, current question, wrong count)
- `progress` object — persistent state saved to `localStorage` key `'mathMonsters_v2'` (avatar, collected team, mode completion/stars)
- Question generators (`add20()`, `mult()`, `identFrac()`, `compareFrac()`, etc.) — return `{type, q, ans, choices, hint, walkthrough}`
- `loadQ()` — picks a question for the current mode and renders it
- `submit()` — checks answer, applies HP damage, triggers hints or victory/defeat flow
- Scratchpad — Canvas 2D drawing overlay (pointer events, pen/eraser)
- Arcade — Space Invaders bonus game using `requestAnimationFrame` + Canvas

## Game Flow & Mechanics

- **Modes**: Warm-Up (addition/subtraction/place value), Main Battle (multiplication/division), Fractions, Team Battle
- **Unlock chain**: Warm-Up → Main Battle → Fractions; Team Battle unlocks after collecting 5+ monsters
- **Round**: 3 monsters per round; each monster has 4 HP (Team Battle enemies have 6)
- **Correct answer**: enemy takes 1 damage; **wrong answer**: hero takes 1 damage + hint shown; 2+ wrong → auto walkthrough
- **Victory**: defeated monster is recruited to `progress.team`
- **Stars**: ≥90% correct → 3 stars, ≥70% → 2 stars, else 1 star

## Question Types

Questions have a `type` field:
- `'type'` — text input (`<input>`)
- `'choice'` — multiple-choice buttons
- `'choicefrac'` — fraction visual + choice buttons

## CSS Conventions

CSS variables defined in `:root` — use these for all colors/spacing rather than hardcoding values:
- `--bg`, `--card`, `--card2` — backgrounds
- `--accent` (pink), `--accent2` (cyan), `--accent3` (gold) — brand colors
- `--correct` (cyan), `--wrong` (red), `--hint` (gold)
- `--radius: 16px`

## Avatar System

12 avatar options defined in the `AVATARS` array. Each has `{id, label, sprite}` where `sprite` is an emoji string. The selected avatar id is stored in `progress.avatarId`.
