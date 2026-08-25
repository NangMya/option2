# Treasure Radar

A prototype outdoor AR treasure hunt in a single `index.html` (no build step).

**Live flow**

1. Real-time distance on radar + metal-detector audio (nearest of **3** treasures)  
2. At **≤ 20 m** — higher-pitched detector + **Open AR Camera** CTA  
3. User opens AR, scans surroundings  
4. At **≤ 10 m** — treasure locks **on screen** (stays visible while you pan) — tap to open  
5. After all **3** opened → **Victory Selfie**: real you (front camera) + AR treasure box in frame  
6. Form: **Name** + **Phone** → **Share to Claim**

**Movement is GPS-only** in real play. Shake does nothing. Compass only sets facing.

---

## Running it

**Do not** open the HTML as a local file on a phone — GPS is blocked for `file://`. Use **HTTPS**.

- **Phone (real play):** `https://your-domain/…/index.html` — allow Location, walk outdoors.  
- **Sandbox (seated):** `?debug=1&near=1` — D-pad / WASD + Debug QA panel (no outdoor walk).

---

## Sandbox / debug mode

No real GPS. Move on the radar with **WASD / arrow keys** (PC) or the on-screen **▲◀▼▶** pad. There is **no Debug QA / warp card** — walk to each of the **3** treasures yourself.

```
https://your-domain.com/index.html?debug=1&near=1
```

| Flag | What it does |
| --- | --- |
| `?debug=1` | No GPS — keyboard / D-pad movement |
| `&near=1` | Treasures spawn **~8–18 m** away |
| `&at=15` | Spawn treasures around **N meters** ahead |

**Seated test:** PLAY → walk with pad toward the yellow arc → ≤20 m detector + **Open AR Camera** → ≤10 m tap chest → repeat for all **3** → **Take Victory Selfie** → claim.

Radar + detector work from a local file in debug mode. **Camera** still needs HTTPS / localhost on most browsers.

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
