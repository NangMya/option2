# Coffee Mix Weekly AR Hunt

City-wide weekly AR treasure hunt prototype in a single `index.html` (no build step, no backend).

**Campaign flow (Steps 2–5 in product; Step 1 social entry skipped in app)**

1. **PLAY** unlocks GPS + metal-detector audio (live) or sandbox movement (debug)
2. Real-time distance to the **weekly drop coordinates** + detector beeps
3. At **≤ 20 m** — tap **Open AR Camera**, pan to find the chest at the drop site
4. At **≤ 8 m** + aim — **OPEN CHEST** → **Victory Selfie** → Name + Phone
5. Branded share card + **verification code** → post on Facebook/Instagram + comment on official weekly post

**Prizes (campaign copy):** fastest finder wins the weekly grand prize; next 50 finders get coffee gift sets.

Claims are **client-only** in this prototype (localStorage). Ops match verification codes from public posts manually until a backend is added.

---

## Weekly ops

Edit the config block at the top of [`index.html`](index.html):

```js
const WEEKLY_DROP = {
  weekId: "2026-W35",
  label: "Coffee Mix Weekly Hunt",
  lat: 16.8661,   // drop latitude — update each week
  lng: 96.1951,   // drop longitude
  socialPostUrl: "https://www.facebook.com/...", // official post to comment on
};
```

Deploy to HTTPS, QA with `?test=1`, then field-test at the drop site.

---

## Running it

**Do not** open the HTML as a local file on a phone. GPS and camera need a secure context (`https://` or `localhost`).

**Recommended:** GitHub Pages, Netlify, Vercel, or any static HTTPS host.

- **Phone (real play):** open your HTTPS URL → **START HUNT** → allow Location → walk to weekly coordinates
- **Sandbox (seated QA):** append `?test=1` (same as `?debug=1&near=1`)

### Local PC only

```powershell
python -m http.server 8123
```

Then open `http://localhost:8123/?test=1`

---

## Debug / test links

The start screen shows a copyable QA link. Aliases:

| URL | What it does |
| --- | --- |
| `?test=1` | Sandbox: no GPS, treasure ~8–18 m ahead, D-pad / WASD |
| `?test=1&at=15` | Spawn treasure exactly **15 m** ahead |
| `?debug=1&near=1` | Same as `?test=1` |

Example:

```
https://your-site/index.html?test=1
https://your-site/index.html?test=1&at=15
```

**Seated test:** START HUNT → walk with pad toward bearing arc → ≤20 m detector + **Open AR Camera** → ≤8 m open chest → selfie → claim → social overlay.

---

## Zones

| Zone | Distance | Behavior |
| --- | --- | --- |
| Cold | > 20 m | Distance HUD; detector quiet |
| Hot | ≤ 20 m | Faster / higher beeps + **Open AR Camera** |
| Place | ≤ 8 m (AR open) | Aim + **OPEN CHEST** |
| Flash | ≤ 6 m | Corner strobes + vibrate (screen on) |

AR does **not** auto-open; tap **Open AR Camera** in the hot zone. **← RADAR** exits AR (stream stays warm).

---

## Controls

| Input | Action |
| --- | --- |
| Walking (GPS) | Moves you on the radar 1:1 |
| Compass | Facing for AR aim |
| **Open AR Camera** | Manual AR at ≤20 m |
| Tap AR chest / OPEN CHEST | Open weekly treasure |
| **Share to Claim** | Branded card + verification code |
| `?test=1` pad / WASD | Seated sandbox movement |

D-pad is **hidden** unless debug/test mode.

---

## Saved data

`localStorage` key prefix `coffee_mix_hunt_save`:

- `coffee_mix_hunt_save` — best time, runs
- `coffee_mix_hunt_save_claim` — last claim with `verificationCode`, `weekId`, name, phone

---

## Phase 2 (not in this build)

- Server-side claim validation and fastest-finder leaderboard
- True WebXR / 3D chest model (current AR uses rear camera + compass world-aim)
- Step 1 social microsite landing (skipped; app starts at PLAY)
