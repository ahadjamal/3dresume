# The Creative Trail

An interactive 3D resume for Ahadh Jamal M J. You drive a small off-road car through a Wayanad-inspired, low-poly landscape and discover career chapters, skills, a portfolio gallery and contact details along the way.

## Run it

Everything lives in a single file, `index.html`. Three.js 0.160 loads from the jsDelivr CDN. Serve the folder over HTTP, because ES-module import maps don't work from `file://` in every browser:

```bash
python -m http.server 8123
```

Then open http://localhost:8123.

## Editing content

All portfolio content is data at the top of the `<script type="module">` block:

| Constant | What it holds |
|---|---|
| `CONFIG.linkedin` | The verified LinkedIn URL. While it's empty, no LinkedIn link is shown. |
| `PROFILE` | Name, identity line, email, phone and summary. |
| `PLACES` | The eight destinations: position, pad size and beacon height. Panel text is in `PLACE_CONTENT`, `HUB_STEPS`, `openContact()` and `openGallery()`. |
| `COLLECTIBLES` | The 24 skill tokens: `at:[road, t, sideOffset]` sets where each one sits along a road. |
| `SKILL_GROUPS` | Resume skill groups shown at the Observatory. |
| `GALLERY` | The seven gallery displays. Add real work to `projects: [{ title, brief, role, tools, process, outcomes }]`; empty fields stay hidden. Until real work is added, each display is labelled as an illustrative study. |

## Controls

W A S D / arrow keys to drive, Space for the handbrake, E to interact, R to recover the car, M for the minimap, J for the journal, L for headlights, Esc to close a panel. Drag to orbit the camera and scroll to zoom. On touch devices, on-screen pedals appear, along with drag-to-orbit and pinch-to-zoom.

## Implementation notes

- **Physics** is a purpose-built rigid-body vehicle instead of cannon-es. It runs a fixed 120 Hz step with 4-wheel raycast suspension, a tyre friction ellipse, speed-dependent steering and inertia-tensor dynamics. Static colliders (circles and oriented boxes) sit in a spatial hash, and rendering interpolates between physics steps. Because it raycasts directly against the analytic heightfield and the bridge decks, there's no mesh-collision tunnelling and no extra dependency.
- **World**: a procedural heightfield with river valleys and a pond. Roads are smoothed and grade-limited, and the terrain is cut and filled to meet them. Bridges are generated wherever a road crosses the stream. Vegetation is instanced, chunked for frustum culling, and sways in the wind via a shader.
- **Quality scaling**: on touch or low-core devices, pixel ratio, shadow-map size and vegetation density are reduced. Resolution also steps down if the frame rate drops during the first few seconds.
- Progress (discovered places, collected skills, explored map area, sound and minimap preferences) is stored in `localStorage`. The journal has a reset button.
- Add `?debug` to the URL to expose `window.__trail` test hooks (`visit`, `advance`, the vehicle and so on).
- Add `?selftest` to run an automated check on load. It drives into every prop stack, drives every road and driveway in both directions, crosses every bridge at speed, and does 3 minutes of random off-road driving. The results print on screen.
- **Physics props** (blocks, laterite bricks, tea crates, cones) use cannon-es. The car is a kinematic pusher, so props never destabilise the driving model. A disturbed stack rebuilds itself once the car is about 75 m away.
