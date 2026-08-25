# Flappy FPV

Two one-finger games on one page, no dependencies. The home screen picks one,
and each keeps its own best score locally in the browser.

**Flappy FPV** — first-person 3D Flappy Bird. Tap (or press space) to flap and
fly through the gaps.

**Ring** — a hoop threaded on a wire. Hold to lift it, let go and it drops; the
wire has to keep running clean through the hole. Touch the band and the run is
over. The hoop reddens as the wire nears the metal, which is the only warning
you get.

## Run it

Open `index.html` in a browser, or serve the folder:

```
python3 -m http.server 8000
```

Then go to http://localhost:8000 — it is built for mobile (portrait).

## How it works

Everything lives in `index.html`: a home screen and two games sharing one
renderer, one bitmap font and one audio path.

### The renderer, shared by both

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

### Ring

The hoop is modelled as a tube the wire has to stay inside: the wire is sampled
at five points across the hoop's depth and the worst offender decides. That one
rule gives the game its shape — a steep run is hard because the wire crosses
the whole tube, so the hoop has to sit dead on the slope, not merely near it.

The wire is a polyline built one node ahead of the hoop, steepening with the
score and folding back on itself rather than leaving its lane. The hoop's lift
and the scroll are expressed as fractions of the buffer height, so the game
feels the same on any screen.

A bang-bang autopilot — hold when the wire is above the hoop, release when it
is below — survives about 32 seconds and 39 turns before the slopes beat it.

### Flappy FPV: playing fair

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
