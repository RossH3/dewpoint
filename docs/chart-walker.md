# Chart walker (Easter egg) — design notes

A small figure who walks the 7-day chart and reacts to the weather he walks into. Hidden but easy to find; never in the way.

Status: **design + wireframe** (`docs/walker-sketch.html`). Not in `index.html` yet.

## Finding him
- A tiny figure icon at the "Now" corner of the chart (or triple-tap the droplet logo).
- Tap: he walks Now → end of forecast in ~25 s, waves, and fades out. Tap again to replay. Tap him mid-walk to pause.

## The path
- **Temperature line by default**: the daily highs/lows are the best hills.
- **Hops to the dew point line** where the two nearly meet (spread ≤ 2°: saturated, foggy/rainy air) and wades there; hops back up when they part.
- Follows the same smooth curve the chart draws (sample the SVG path with `getPointAtLength`), so he's exactly on the line.

## Poses (all one SVG figure, parts toggled by class)
| Pose | When |
|---|---|
| Walk | default; leg/arm swing |
| Climb | slope up > ~25°: lean forward, slower |
| Slide | slope down > ~25°: sits, arms up, faster |
| Umbrella | rain chance overhead ≥ 30%; hurries ≥ 60% |
| Bonk | a brief-shower raindrop hits his head |
| Sunglasses + "Ahh, nice." bubble | inside a Nice day |
| Sweat drop + brow wipe | dew point Muggy or worse (≥ 65°) |
| Hat blows off / caught | passing the pressure L / the next H |
| Lantern, slower | night hours |
| Skip | weekend |
| Wave | end of the walk |

## Radar mode
- Stands on the place marker on the radar map. Umbrella opens when the nearby-rain check says rain is within ~10 mi or overhead; sunglasses when the next nice day is today.

## Build notes
- One inline SVG (~20 px tall on the chart), drawn from simple shapes: no image files.
- Animation: `requestAnimationFrame` moves him along the path; CSS keyframes swing limbs; poses are classes on the figure.
- Reactions come from data the page already has (`ps[i].pop`, `dew`, `temp`, `niceKeys`, `presPivots`, `night`, weekend) at the hour under his feet.
- `prefers-reduced-motion`: he appears at "Now" and doesn't walk.
- Pauses when the tab is hidden. No new requests.
- Later: optional sound (footsteps, rain patter), off by default.

## Build order (next session)
1. Figure + walk/climb/slide on the temperature line.
2. Umbrella, sweat, "Ahh, nice."
3. Dew-line hop, bonk, hat, lantern, skip, wave.
4. Radar mode.
