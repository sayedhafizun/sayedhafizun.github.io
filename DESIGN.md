# Design

Recorded from the built site, not from intention. `index.html`,
`assets/css/site.css`, `assets/js/site.js`, `assets/js/scene.js`.

## World

Light-first, document-grade, engineered. The page reads like a well-set
technical document that happens to be alive: hairline rules instead of cards,
real tabular data, and one piece of authored motion. Dark theme is a full peer,
not an afterthought.

Chosen against two rejected defaults: the near-black glass/mesh-orb developer
portfolio (rejected by the user as "vibe coded"), and the warm-cream editorial
portfolio. The use scene decided light as the base: a recruiter, daytime, on a
laptop, with a white CV open in the next tab.

## Color

Restrained strategy — neutrals plus one accent. Color never decorates; it marks
the accent action, links, and measured outcomes.

| Token | Light | Dark |
|---|---|---|
| `--paper` | `#FFFFFF` | `#0C0D10` |
| `--surface` | `#F6F7F9` | `#15171C` |
| `--surface-2` | `#EEF0F4` | `#1E212A` |
| `--ink` | `#0B0D13` | `#EDEFF4` |
| `--ink-2` | `#3D4353` | `#BBC1CE` |
| `--muted` | `#656C7E` | `#8B92A2` |
| `--line` | `#E2E5EC` | `#272B35` |
| `--line-soft` | `#EDEFF3` | `#1D2028` |
| `--accent` | `#2450F0` | `#5B82FF` |
| `--accent-deep` | `#1435B8` | `#9DB4FF` |
| `--accent-wash` | `#EEF2FE` | `rgba(91,130,255,.15)` |
| `--ok` / `--bad` | `#067A4E` / `#B3271C` | `#3DD68C` / `#FF6B60` |

`--veil-rgb` carries the page surface into the nav glass and the hero scrim so
both follow the theme. `--scene-node`, `--scene-accent` and
`--scene-line-opacity` are read by Three.js, so the 3D scene never holds its own
color constants.

## Theming

System preference by default. An explicit choice writes `data-theme` on `<html>`
and persists in `localStorage`, winning over the system in both directions:

- `@media (prefers-color-scheme: dark) { :root:not([data-theme="light"]) { … } }`
- `:root[data-theme="dark"] { … }`

An inline `<head>` script applies the stored value before first paint, so there
is no flash. Every storage read and write is wrapped in try/catch for private
mode. While no choice is stored the page keeps following system changes live,
and `themechange` re-tints the 3D scene in place rather than rebuilding it.

## Type

- **Satoshi** (Fontshare) 400/500/700/900 — display and body.
- **JetBrains Mono** 400/500 — `.data` only: dates, durations, and measured
  figures. Never as decoration.

Headings 700, tracking `-0.03em`, `text-wrap: balance`. Display tops out at
`4.5rem`. Body measure capped at 68ch, hero lede at 54ch so it clears the scene.

## Structure

Hairline rules carry the structure. There are no cards and no nested containers.
Experience is a two-column record (dates left, substance right); skills are
key/value rows; contact channels are ruled list items.

Sections use `clamp(5rem, 10vw, 8.5rem)` block padding. `scroll-padding-top: 92px`
keeps anchored headings clear of the sticky nav.

## Motion

**One field, no diagrams.** `scene.js` creates a single WebGL context on a
canvas fixed to the viewport and draws one thing: a 3D particle field behind the
entire page. There is no per-section scene and no graph anywhere.

An earlier build gave every section its own rig — a topology, lanes, a service
stack, a chat ring, an orbiting shell, a growing lattice, a pulse. Only the hero
had any real structure behind it; the other six invented one. Symbols were
chosen because they looked like code, then arranged into flows that did not
parse. All seven were deleted. The rule that replaced them: **a node only exists
where something is genuinely a node.** Nothing on this page currently qualifies,
so nothing here is drawn as a graph.

**What the field does.** 1,800 points (600 on coarse pointers) rest in a cloud
with depth. Each sits at its own brightness, so the field is uneven the way a
real one is. The pointer pushes points away from itself with a force that falls
off over about 5 world units; a pushed point gains speed, and speed drives three
things at once — it stretches into a streak along its own velocity, it swells,
and it brightens from its resting grey toward the accent. When the pointer
leaves, a weak spring returns each point to where it started.

Drawn as two layers over one shared particle state: `THREE.Points` for the
heads, under a custom shader so each point carries its own size rather than
sharing one, and `THREE.LineSegments` for the streaks, two vertices per point
with the second trailing along the velocity. The streak is real geometry, not a
blur filter.

Scrolling drifts the field vertically and eases the camera in, so the motion
continues the whole way down the page without anything being invented for each
section.

Structure animates as it arrives and then stops: section rules draw in, each
metric loads its own accent bar and counts up, measured results wipe their
highlight, bullets and tags arrive in sequence (`--i` set in JS), and a
read-progress rail tracks the nav. The only loops are the two things that
represent something live — the availability dot and the current-role chip.

Guards: `prefers-reduced-motion` stills the field entirely and settles the page
in its arrived state; coarse pointers get a smaller, calmer field with no
pointer response; DPR capped at 2; WebGL failure sets `data-no-webgl` and leaves
the page fully intact.

## Cursor

A rounded navigator arrow in liquid glass, traced off a reference rather than
invented. The silhouette was read out of the reference image pixel by pixel:
a **vertical left edge**, the nose out to the right at ~70% of the height, and
a rounded point at the bottom — not a flat cut. Interior angles 47° at the tip,
64° at the nose, 69° at the bottom; bounding box 58×77, so it stands upright
rather than leaning. Corners are filleted with real circular arcs (2.4 at the
tip, 3.2 at the nose, 3.0 at the bottom) inside a 34px box, and the identical
path string is used as the `clip-path` and as the SVG rim so the glass and its
edge can never drift apart. Hotspot `3 2`, the tip.

**It refracts, it does not fake it.** The pane carries `backdrop-filter:
blur(1.5px) saturate(180%) brightness(1.01)`, so it samples whatever is behind
it — type, the accent buttons, the particle field — and bends it rather than
hiding it. Clear glass, not frosted: the blur is small enough that you read
straight through the pane, and the saturation lift is what signals a material
at all. Almost nothing sits on top either; the tint runs `--glass-hi` .085 down
to `--glass-lo` .003 in light, half that again in dark, and the brightness lift
is near-neutral. At 1.1 it pushed everything behind the pane toward white,
which reads as opacity over dark type however thin the fill is.

**The rim is what makes it findable.** A body that transparent cannot carry the
shape by itself, so the edge does: one crisp ring at 1.7px that flips with the
theme — white on dark, ink on light — with a bevel falloff along its length, a
wider low-opacity ring under it for contact, and a soft drop-shadow. The
drop-shadow sits on the rim element rather than the wrapper on purpose: an
ancestor `filter` creates a backdrop root and would stop the pane sampling the
page at all.

An earlier pass carried a heavy 3.4px dark slab in light mode. It was findable
and it looked wrong. The fix was to make both themes the same crisp ring rather
than compensating for paper with weight.

Hovering anything interactive scales it 1.28 from the tip and deepens the blur.

**Why this one is JavaScript when nothing else is.** A `cursor: url()` is a
static bitmap the compositor stamps down; it can never sample the page beneath
it, so real glass is impossible that way. This is a fixed element moved on
`pointermove`. It tracks the raw coordinates with no easing, so it sits under
the pointer instead of swimming behind it the way a lerped cursor does.

Three guards. It is only switched on when `backdrop-filter` is actually
supported, and `html.lgc-on` — which hides the native cursor — is added by the
script itself, so a blocked or failed script leaves the Remix Icon
`cursor-fill` SVG cursor (Apache-2.0, from the set this repo already vendors)
in place rather than no cursor at all. Over a text field the pane hides and the
native I-beam returns, because a floating pane cannot show where the caret will
land. And it is off entirely below 860px and on coarse pointers.

## Constraints

Static files only — no build step, no framework runtime. Three.js loads as an ES
module via import map from jsDelivr. Remixicon is vendored locally and is the
only icon source; stroke weight is uniform and no emoji or Unicode glyphs stand
in for icons.

## Refused

Eyebrow/kicker labels, section numbers, gradient text, decorative glass or blur,
nested cards, icon-heading-text card grids as page structure, self-assigned
skill percentage bars, and monospace as a "technical" costume.
