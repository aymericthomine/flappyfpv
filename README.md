# Daily Mini Golf

Nine holes of 2D mini golf, generated from the date. One page, no dependencies,
no build step. Everyone who opens it on the same day plays the same course.

Pull back from the ball and let go. Sand slows you down, water costs a stroke,
the red bumpers are lively. Eight strokes and the hole is picked up.

## Run it

Open `index.html` in a browser, or serve the folder:

```
python3 -m http.server 8000
```

Then go to http://localhost:8000 — it is built for mobile (portrait).

## How it works

Everything lives in `index.html`: the generator, the physics, the rendering
and the leaderboard.

### The course of the day

The date is the seed. `2026-09-02` hashes into a stream of numbers, and the
same nine holes come out of it on every device in the world — no server, no
agreement, no syncing. Tomorrow's course is already decided and nobody has
seen it.

Holes are composed of rectangles and discs only, which keeps the collisions
honest and the drawing flat. Each one picks a length class first (a short hole
puts the tee under the cup, so "short" does not mean "diagonally across the
green"), then a shape — open, block, gate, baffle, corridor or pocket — placed
**between the tee and the cup**, because an obstacle anywhere else is scenery.
Sand, water and bumpers are layered on top, more of them as the round goes on.

Par is read off the finished hole rather than decided in advance: how far it
is, and how much is in the way.

### Nothing unplayable ships

A course nobody can finish is worse than a boring one. Every generated hole is
flood-filled on a coarse grid, inflated by the ball's radius, and only kept if
the ball can actually roll from the tee to the cup — otherwise the seed moves
on and the hole is generated again, deterministically.

Checked over two years of dates: 6570 holes, all reachable, the fallback hole
never used, and par totals between 26 and 34 with a median of 30.

### Physics

Exponential damping (much stronger in sand), eight substeps per frame so
nothing tunnels through a wall, and collisions resolved against the closest
point of each rectangle — which handles corners without special-casing them.
The cup only takes the ball below a speed threshold; above it the ball is
tugged toward the centre and rolls on, which is what a lip-out feels like.

### The leaderboard

It is local, and the code says so plainly: every player on this device gets a
line, best score per name, cleared when tomorrow's course opens. That is a real
board for a phone passed around a table, and an honest one — a shared board
needs a server, and swapping `readBoard` / `writeBoard` is the whole job.

The result also copies out as a spoiler-free line, one square per hole:

```
Daily Mini Golf #245
29 (-1)
⬜🟩🟨⬜🟥🟩⬜🟨⬜
```
