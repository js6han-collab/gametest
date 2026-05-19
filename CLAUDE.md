# game_interaction — TPS Loot Prototype

## What this is

Third-person shooter prototype for **interaction/UI validation + dev demos**. Not aiming for production quality. User explicitly framed it as "프로토타입 빠르게 검증 + 동료에게 공유/시연" — speed of iteration and ease of sharing dominate over depth, polish, or architecture.

## Stack

Single-file vanilla HTML/CSS/JS with **Three.js r128 from CDN**. Vite is a thin dev-server wrapper (HMR + LAN host + static build).

- Source of truth: [시뮬레이터_루팅_시스템_Prototype.html](시뮬레이터_루팅_시스템_Prototype.html) (~7350 lines, ~318 KB)
- Entry redirect: [index.html](index.html) (just redirects to the Korean-named file)
- Vite config: [vite.config.js](vite.config.js)
- Dev: `npm run dev` (add `-- --host` for LAN sharing)
- Build: `npm run build` → `dist/`

**Do NOT** propose ES module extraction, npm `three` migration, TypeScript adoption, or framework introduction (React/Vue) unless explicitly asked. Three.js API choices must stay r128-compatible (June 2021 — pre color-management, pre intensity changes).

New features → inline `<script>` / `<style>` in the existing prototype HTML. Vanilla DOM, `"use strict"`, global scope.

## Figma source of truth

The full-screen inventory (`#fullinv` / `.fig-canvas`) is a pixel-perfect mirror of Figma file `3CzR6SL2LtlGXReIuatj5k` ("PUBG-V UX Part") node `4030:4471` (3840×2160 with `transform: scale()` viewport fit).

Frame layout:
- Top tab strip (인벤토리/맵/설정) y=0..200
- 3 columns at y=265..1917:
  - 주변 (left, x=128, 862w): 바닥 sub-section (컨테이너는 별도 패널)
  - 배낭 (center, x=1912): 배낭 grid + 안전 포켓 (296px gap)
  - 장비 (right, x=2852): 무기 (top, primary+secondary 860×480 cards) + 방어구 (bottom, 3 slots) — 방어구는 안전포켓과 horizontally 정렬됨 (margin-top 165px)
- T-pill footer y=1982..2160

Treat the `--fig-*` token block (lines ~10–170) as the authoritative design-token source.

JS references inventory elements by `id` (`fiInvGrid`, `fiWeaponPrimary`, `fiEquipGrid` …), so DOM reordering inside `.fig-canvas` is safe.

## Figma assets — [public/figma-assets/](public/figma-assets/)

Lives under Vite's `public/` so the build auto-copies the whole tree to `dist/figma-assets/` — no manual `cp` needed, and `netlify deploy --prod --dir=dist` works without `--no-build`. HTML references stay as relative `figma-assets/...` (dev server serves `public/` at root).

PUBG-style icons from [pubg.wiki.gg](https://pubg.wiki.gg) downloaded into `pubg-icons/`:
- Weapons: rifle, smg, sniper, shotgun, pistol
- Armor: helmet, vest, pack
- Heal: bandage, firstaid, medkit, energy, pain, adren
- Throw: frag, smoke, flash, molotov
- Ammo (per caliber): ammo556, ammo762, ammo9mm, ammo45, ammo12g

Other Figma assets: `focused-border.svg` (the rainbow stroke — now replaced at runtime by CSS gradient + mask for proper stretching), `cell-accent.svg`, `bumper-{lb,rb}.svg`, `trigger-{lt,rt}.svg`, `notif-dot.svg`, `weapon-m16.png`.

## Slot visual system (current)

- All slots use border-radius `--fig-cell-radius: 20px` and the dual-background gradient-border pattern (transparent border + `padding-box` body + `border-box` stroke). This keeps rarity stroke gradients while preserving rounded corners — **avoid `border-image` here**, it ignores border-radius.
- Focus border = CSS gradient + `mask-composite: exclude` (4px ring). Same pattern on `.fi-cell`, `.fi-weapon-slot`, `.weapon-attach`. The ring's inner curve equals slot radius, outer curve = slot radius + 4 → concentric.
- `.fi-pad-focus::before` is gated by `body.pad-mode` so the initial pad-focus on inventory open doesn't show a focus border in KB+M mode.
- Category icons (bottom-left `cell-cat-icon`) are simple white silhouettes inlined in `CAT_ICON_SVG`. Not tinted by rarity.

## Interaction rules (cheat sheet)

- **PC E hold** (inv open) = pickup all nearby items. Tap with ground focus = single pickup.
- **Pad X hold** (inv open) = pickup all if ground focus, else drop focused item.
- **Double-click** any slot → quickMove (auto-equip / auto-unequip / pickup).
- **Drag** an attachment slot inside a weapon card = drags ONLY that attachment (stopPropagation in `onFIDragStart`). Drag the weapon body = drags the weapon.
- **Unequipping a weapon** to backpack/floor/container automatically detaches its attachments — they become separate items in the destination. See `detachAllFromWeapon()` + the `detached` distribution in `performTransfer`.

## Conventions

- Korean comments are fine.
- Memory file `MEMORY.md` lives at `~/.claude/projects/c--Users-pc-game-interaction/memory/` on the home machine — **does not sync via git**. Bootstrap context from this CLAUDE.md instead.
- If you make non-trivial changes, update relevant sections of this file before committing.

## Sync workflow (home ↔ work)

```bash
# At home, after a session:
git add -A && git commit -m "…" && git push

# At work, fresh session:
git pull
npm install   # if node_modules missing
npm run dev
# Claude Code at work auto-loads this CLAUDE.md for context.
```

## Known follow-ups

- File is heavy (214 KB inline JS in one `<script>` block; 32 KB inlined base64 PNG for `metal_parts` on line ~3201). Extracting JS to `src/prototype.js` and `metal_parts` to a PNG file would speed up future edits significantly. User deferred this refactor.
