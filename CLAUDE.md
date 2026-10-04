# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A collection of small, independent browser games, each a **single self-contained HTML file** (inline CSS + JS, no build step, no package manager, no tests). UI text, identifiers and comments are mostly in **Brazilian Portuguese** (`lang="pt-BR"`); keep new text in Portuguese.

| Folder | Game | Tech |
|---|---|---|
| `bombeiro_hercules/index.html` | Hércules Bombeiro — C-130 firefighting flight sim (scoop water, drop on building/forest fires) | Three.js r128 |
| `missoes_hercules/index.html` | Rota das Iguanas — C-130 cargo/transport missions between airports | Three.js r128 |
| `resgate_hercules_iguanas/index.html` | Hércules de Resgate — 2D side-scroller rescuing iguanas | Canvas 2D |
| `penalti_vovo/penalti-vovo-jonas.html` | Pênalti do Vovô Jonas — penalty shootout vs. grandpa goalkeeper | Three.js r128 |

The root `index.html` is a kid-friendly launcher page ("Meus Jogos"): one big card per game, each with an inline-SVG icon, linking to the paths above by relative URL. **When adding, renaming or removing a game folder, update its card in `index.html`** (each card is an `<a class="jogo …">` with a per-game `--cor` color class).

## Running

Open `index.html` (or any game's HTML file) directly in a browser (or serve the folder with any static server, e.g. `python -m http.server`). External dependencies are loaded from CDNs only: Three.js r128 from `cdnjs.cloudflare.com` and fonts from Google Fonts — keep it that way (no local assets, no bundler).

## Architecture notes

- **The two flight sims share a large copy-pasted core.** `bombeiro_hercules` and `missoes_hercules` were forked from the same code: world data (`AIRPORTS`, `ISLAND`, coastline/water helpers like `isWater`, `local`, `onRunway`, `airportAt`), the renderer setup and geometry helpers (`lam`, `layerMat`, `mergeParts`, `strip`, `ribbon`), terrain/city/tree instancing, runway/sign textures, the procedural C-130 model (`/* C-130 HERCULES */` section), FX, flight physics, audio and keyboard controls. They then diverge in the game-specific section (fires/tank/`POUR_RATE` vs. cargo `SITES`/`CARGO`/`GEN` mission generators). A bug fix or improvement in the shared core usually needs to be applied to **both** files by hand.
- Code in those files is organized by `/* ================== SECTION ================== */` banners (WORLD DATA, SAVE, RENDERER, C-130 HERCULES, GAME STATE, FIRES, AUDIO, …) — use them to navigate. Style is dense, minified-looking JS; match it.
- Persistence is `localStorage` wrapped in `try/catch`, with per-game keys (`hercules-bombeiro-v1`, `rota-iguanas-v1`, `herc_best`). Bump the version suffix if the save shape changes incompatibly.
- Sound is synthesized with the Web Audio API (oscillators), created lazily on first user interaction — there are no audio files.
- Games support both keyboard and pointer/touch input and are meant to work on mobile (viewport-fit, safe-area insets).
- `bombeiro_hercules/index.html` begins with an extra wrapper `<head>`/`<style>` prelude (from being exported as a published Artifact) before its own document; be careful when editing the top of that file.
