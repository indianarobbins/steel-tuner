# Steel — site

Landing page for **Steel**, a pedal steel tuner that derives each string's
target from the chord it is voicing rather than from equal temperament.

Live at <https://indianarobbins.github.io/steel-tuner/>.

## What's here

- `index.html` — the whole page. No build step, no dependencies, no tracking.
- `data.json` — the interactive chart's numbers.

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
