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

### Evolving scenery

Every 8 points the world crosses into the next zone — Neon, Sunset, Abyss,
Toxic, Glacier, Ember — and cycles. Each zone carries its own sky gradient,
void, floor, pipe and skyline colours. The live palette eases toward the
target zone every frame, so the world shifts continuously rather than
snapping, and the zone name flashes under the score as you enter it.
