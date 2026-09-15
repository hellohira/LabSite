# Hero motion prototype

A single self-contained file — `hero-motion.html`. No build step, no
dependencies, no framework. Open it and it runs.

## Run it locally

Opening the file directly (double-click, `file://…`) works for everything
except video cards, which some browsers refuse to autoplay from `file://`.
Serving it avoids that and matches how it will behave in production:

```sh
cd prototypes
python3 -m http.server 8000
```

Then visit <http://localhost:8000/hero-motion.html>.

Any static server does the same job — `npx serve`, `php -S localhost:8000`,
the VS Code Live Server extension. Nothing here needs Node.

## Add your photos

1. Drop files into `prototypes/images/`.
2. Open `hero-motion.html` and find the `var CARDS = [` block — it's the only
   thing you need to edit. Set `src` on each row:

```js
{ src: 'images/neon-drift.jpg', n: 'Neon Drift', g: 'Racer',
  x: -720, y: -320, w: 360, h: 250, d: 0.55, r: -3.5, hue: 268 },
```

Field reference:

| field | what it does |
|-------|--------------|
| `src` | path to the image. `''` keeps the gradient placeholder. A `.mp4` / `.webm` path becomes a muted, looping, autoplaying video. |
| `n` / `g` | caption title and genre. Set `n: ''` for no caption. |
| `x` / `y` | resting position in px from the centre of the hero. |
| `w` / `h` | card size in px at a 1920-wide viewport; scales down from there. |
| `d` | depth, `0.3` (far, slow, small) to `1.4` (near, fast, big). |
| `r` | in-plane rotation in degrees. Keep it within ±6. |
| `hue` | placeholder colour only — ignored once `src` is set. |

Add or remove rows freely; nothing is hardcoded to twelve cards.

A missing file falls back to the placeholder rather than showing a broken
card, so you can fill the set in one at a time.

### Preparing the files

Every card sits in the first screen, so all of them load immediately — keep
the total weight down.

- Export at roughly **2× the card's `w`** (a 360px card wants ~720px) and no
  wider than ~900px. Anything beyond that is invisible detail at these sizes.
- **WebP or AVIF**, quality ~75. Expect 40–80KB per card.
- Twelve cards should total well under 1MB. If it doesn't, the images are
  too large.
- Cards crop with `object-fit: cover`, so keep the subject centred — the
  edges get cut at some card ratios.

### Video cards

Muted, looping and `playsinline` are set for you. Keep clips short (3–6s)
and quiet — more than about six decoding at once will cost you framerate on
a laptop. Add `poster: 'images/still.jpg'` alongside `src` for the first
frame.

## Change the font

Two edits, both at the very top of the file.

1. The Google Fonts `<link>` — swap the families in the URL.
2. The `--display` and `--ui` tokens in `:root` — match the names, and keep
   a real fallback stack after each one so the page doesn't reflow when the
   webfont is slow.

```css
--display: "Your Display Face", "Helvetica Neue", Arial, sans-serif;
--ui:      "Your Text Face", "Helvetica Neue", Arial, sans-serif;
```

Nothing else references a font name, so those two lines change the whole
page.

## Tuning the motion

The control pill along the bottom is a development affordance, not part of
the design — delete the `<div class="panel">` block when you're happy with
the values, and hardcode them in the `state` section of the script.

| control | variable | what it changes |
|---------|----------|-----------------|
| Pull | `strength` | how hard cards are drawn to the cursor |
| Reach | `reachPct` | radius of influence around the cursor |
| Spread | `spread` | parallax travel (scatter) / ring radius (cylinder) |
| Ease | `ease` | damping. Lower is smoother and heavier. |
| Fill | — | placeholder gradients, for cards with no `src` |

There's also a faint dot and halo tracking the cursor so you can see the
reach while tuning. It's the `.field` element — delete it, or keep it as a
custom cursor.
