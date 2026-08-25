# Treasure Radar

A prototype outdoor AR treasure hunt in a single `index.html` (no build step).

**Live flow**

1. Real-time distance on radar + metal-detector audio  
2. At **≤ 20 m** — higher-pitched detector + **Open AR Camera** CTA  
3. User opens AR, scans surroundings  
4. At **≤ 10 m** (place geofence) — animated treasure chest appears on the ground plane  
5. Tap chest to open → **Take Victory Selfie**  
6. Form: **Name** + **Phone** → **Share to Claim**

**Movement is GPS-only** in real play. Shake does nothing. Compass only sets facing.

---

## Running it

**Do not** open the HTML as a local file on a phone — GPS is blocked for `file://`. Use **HTTPS**.

- **Phone (real play):** `https://your-domain/…/index.html` — allow Location, walk outdoors.  
- **Sandbox (seated):** `?debug=1&near=1` — D-pad / WASD + Debug QA panel (no outdoor walk).

---

## Sandbox / debug mode

```
https://your-domain.com/index.html?debug=1&near=1
```

| Flag | What it does |
| --- | --- |
| `?debug=1` | No GPS — **WASD** / arrows (PC) or on-screen **▲◀▼▶** pad; shows **Debug QA** panel |
| `&near=1` | Treasure spawns **~8–18 m** away (quick hot-zone reach) |
| `&at=15` | Spawn treasure exactly **N meters** ahead (e.g. `at=5` for place zone) |
| `&skip=ar` | After GO!, jump toward AR (also: `open`, `selfie`, `claim`) |

### Debug QA panel (yellow, bottom-right)

| Button | Action |
| --- | --- |
| **Warp · 15 m** | Put treasure ~15 m ahead (hot zone / Open AR) |
| **Warp · 5 m** | Put treasure ~5 m ahead (place geofence) |
| **Open AR** | Same as the in-game CTA |
| **Force place chest** | Open AR + warp to place zone |
| **Open chest** | Trigger open animation + Victory Selfie CTA |
| **→ Selfie** | Jump to victory selfie stage |
| **→ Claim** | Jump to Name / Phone / Share to Claim |

### Seated full-flow QA (recommended)

1. Open `index.html?debug=1&near=1` (HTTPS or localhost for camera).  
2. Tap **PLAY** → countdown → detector may already beep if spawn is ≤20 m.  
3. Tap **Open AR Camera** (or Debug → Open AR).  
4. **Warp · 5 m** (or walk with ▲) until chest appears → tap chest.  
5. **Take Victory Selfie** → Capture / Use → fill Name + Phone → **Share to Claim**.

**Fastest smoke test:** `?debug=1&near=1&skip=claim` jumps to the claim form after GO!

Radar + detector work from a local file in debug mode. **Camera** still needs HTTPS / localhost on most browsers; debug continues with placeholders if camera is denied.

---

## Testing on a phone (real GPS)

```powershell
python -m http.server 8123 --bind 0.0.0.0
cloudflared tunnel --url http://127.0.0.1:8123 --no-autoupdate
```

Open the `https://….trycloudflare.com` URL → **PLAY** → allow Location → walk until ≤20 m → Open AR → place zone → open chest → selfie → claim.

### If you see “Location permission denied”

1. URL must be `https://`  
2. Browser site settings → Location → Allow  
3. Phone Location ON for the browser  
4. Host must not send `Permissions-Policy: geolocation=()`  
5. Layout-only: `?debug=1` (no GPS)

---

## Zones

| Zone | Distance | Behavior |
| --- | --- | --- |
| Cold | > 20 m | Distance HUD; detector quiet |
| Hot | ≤ 20 m | Faster / higher beeps + **Open AR Camera** |
| Place | ≤ 10 m (while AR open) | Chest placed in camera view |
| Flash | ≤ 8 m | Corner strobes + vibrate (when screen on) |

AR does **not** auto-open; the player must tap **Open AR Camera**. **← RADAR** exits AR (chest re-places when you open AR again in range).

### Background / screen off

Detector keep-alive: Web Audio + HTML5 loop + Media Session + optional Wake Lock. Beeps schedule on the AudioContext clock. GPS may still update while media plays (device-dependent).

---

## Controls

| Input | Action |
| --- | --- |
| Walking (GPS) | Moves you on the radar 1:1 |
| Compass | Facing only |
| **Open AR Camera** | Manual AR entry at ≤20 m |
| Tap AR chest | Open treasure (in place zone) |
| Victory Selfie / Claim | End loop |
| `?debug=1` pad / WASD | Seated movement |

D-pad is **hidden** unless `?debug=1` (anti-cheat).

---

## Anti-cheat notes

- No accelerometer movement  
- Real play: position only from `watchPosition`  
- Accuracy worse than ±50 ft ignored; hops under 3 ft ignored  
- Start GPS fix = field center; treasure is a virtual offset (debug can warp it)

---

## Saved data

`localStorage`:

- `treasure_radar_save` — best time, points, runs  
- `treasure_radar_save_claim` — last Name / Phone claim payload  

---

## Notes

Earlier Next.js + Prisma + pedometer version was removed. This file is the full prototype.
