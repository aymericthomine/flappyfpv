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

- everything is drawn into a small buffer — about 260 pixels tall — and blown
  up with hard edges, so the 3d engine produces pixel art rather than smooth
  polygons; the score and the cards use a 5×7 bitmap font drawn into the same
  buffer, so every readout sits on the world's pixel grid
- hand-rolled perspective projection, with camera pitch
- near-plane polygon clipping (`clipNear`) for the floor and ceiling, which
  pass behind the camera
- pipes drawn as boxes, back faces culled, painter's algorithm for depth
- separate distance fog for the corridor (which dissolves into the void) and
  for the pipes (which stay readable), quantised into six flat bands so depth
  reads as steps rather than a blur

### Drawing it like pixel art, not like a small screenshot

The buffer alone only makes the pixels big. What makes the picture read is
craft applied per surface:

- the sky is **ordered dithering**, not a gradient: three colours through a
  4×4 Bayer matrix give nine scattered tones, rebuilt only when the eased
  palette has actually moved
- the corridor is a **checkerboard anchored in the world**, so it streams past
  instead of sitting still under you; the dark tiles are mixed a fifth of the
  way toward the void, which ties the floor to the sky
- the ceiling carries **light panels** at a fixed spacing — it is half the
  screen and had nothing to say, and they double as the clearest read on speed
- each pipe gets a **lit band and a shaded band** down its face: a flat front
  reads as cardboard, two more quads read as a tube
- the near towers have **lit windows**, which is the difference between a
  skyline and a row of shapes
- every colour lands on a rung of a 32-step ladder — a limited palette is what
  separates pixel art from a small screenshot

Zone colours are still generated, but only the hues roam. Saturation and
lightness are held in narrow bands with a fixed value structure — a bright sky
over a deep corridor, every time — because letting them roam is exactly what
produced the washed-out zones and the muddy ones.

### Playing fair

Three rules keep a death felt like your own fault:

- the ceiling is a wall you scrape along, not a kill plane — only the floor
  and the pipes end a run
- the pipes test a smaller box than they draw, so a hair's breadth reads as a
  hair's breadth
- the next gap's opening is outlined faintly, fading in from far away and out
  again once you are committed: in first person, judging how high the gap sits
  is the hard part, and guessing is not the game

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
