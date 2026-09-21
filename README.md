# Steel Tuner — site

Landing page for **Steel Tuner**, a pedal steel tuner that derives each string's
target from the chord it is voicing rather than from equal temperament.

Live at **<https://steeltuner.com>**, served by a Cloudflare Worker
(`steeltuner`) via Workers Routes on `steeltuner.com/*` and
`www.steeltuner.com/*`.

Deploy with an explicit name, or wrangler names the worker after the current
directory and silently deploys somewhere else:

```sh
wrangler deploy --name steeltuner --assets .
```

GitHub Pages is no longer the live host — the app is public-facing and the
site should not advertise a personal account. Note that `_headers` only works
on Cloudflare; GitHub Pages ignores it and serves no security headers at all,
which is one reason the Worker is the better host here.

`vercel.json` is kept so the repo still deploys to Vercel unchanged, but note
Vercel's Hobby plan is **non-commercial only** and would not cover a paid
app's landing page.

## What's here

- `index.html` — the whole page. No build step, no dependencies, no tracking.
- `data.json` — the interactive chart's numbers.
- `vercel.json` — headers and clean URLs. `data.json` is sent
  `must-revalidate` on purpose, so a cached copy cannot outlive a regenerated
  one and start disagreeing with the app.

## The two animations

Both are canvas, both are in the one `<script>` at the foot of the page, and
both argue a point the copy is already making rather than decorating it.

**The hero trace** is a plucked string arriving sharp and settling onto its
target, because attack and decay are what you actually watch while tuning. The
pitch is a damped spring, not a physical string model — it is the shape the
meter draws, not a simulation. Colour flips to the tuned green inside 1.5¢.

**The beats canvas** is why cents are the wrong unit. Play A110 and the major
third above it: the root's 5th harmonic and the third's 4th harmonic both land
near 550 Hz, and the gap between them *is* the beat rate —

```
beats/sec = 550 * |2^(cents/1200) - 1|
```

At equal temperament the third is 13.686¢ sharp, which is **4.37 beats a
second**. At just intonation it is zero and the envelope goes flat. The
envelope on screen is that true rate; **the carrier is slowed** so it can be
drawn at all, and the caption under the canvas says so.

Both honour `prefers-reduced-motion` by rendering a representative still frame
rather than an empty box — the beating is the argument, so it has to survive
motion being switched off.

## Where the numbers come from

`data.json` is **generated**, not typed. It is the output of the app's own
`SteelExport` target — the same model that drives the tuner — narrowed to the
E9 neck under pure just intonation:

```sh
cd ../PedalSteel
swift run -c release SteelExport > dump.json
```

If a target changes in the app, regenerate rather than editing `data.json` by
hand. Nothing on the page should be able to disagree with the app.

## Local preview

```sh
python3 -m http.server 8765
```

Serve it rather than opening the file directly — `index.html` fetches
`data.json`, which `file://` blocks.
