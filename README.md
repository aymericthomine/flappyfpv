# Flappy FPV

First-person 3D Flappy Bird. One page, no dependencies.

Tap (or press space) to flap and fly through the gaps. The best score is kept
locally in the browser.

## Run it

Open `index.html` in a browser, or serve the folder:

```
python3 -m http.server 8000
```

Then go to http://localhost:8000 — it is built for mobile (portrait).

## How it works

Everything lives in `index.html`: a small 3D engine written on a 2D canvas.

- hand-rolled perspective projection, with camera pitch and roll
- near-plane polygon clipping (`clipNear`) for the floor and ceiling, which
  pass behind the camera
- pipes drawn as boxes, back faces culled, painter's algorithm for depth
- separate distance fog for the corridor (which dissolves into the void) and
  for the pipes (which stay readable)

### Endless scenery

There is no list of zones. Every 104 units flown — about eight pipes — the
corridor crosses into a new zone, and that zone is *generated* from its index:
a golden-angle hue walk picks the dominant colour, deterministic noise picks
the mood, and the skyline gets one of four ways of building itself (blocks,
spires, mesas, leaning stacks) along with its own spread, height and dust
grain. The world therefore keeps changing for as long as you keep flying, and
never loops back to a zone you have already seen.

Zone 0 is the one exception: it is the game's own neon palette, so the first
flight always looks like Flappy FPV.

Two details keep it playable rather than merely colourful:

- pipe lightness is chosen, not assumed — the value whose luminance stands
  furthest from the corridor and the sky, so the gaps stay readable whatever
  the zone came out as
- the live palette eases toward the zone the camera is in, so crossing a
  border is a fade rather than a cut, and towers spawn `SPAWN_Z` ahead, so
  the next skyline rises on the horizon before its colours reach you
