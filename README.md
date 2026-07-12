# skills tracker

Upstream-drift tracking data for skills I authored from scratch but that were
inspired by a popular upstream skill. This repo holds my `watch.json` and the
committed `snapshots/` baseline; the [skills-watcher](https://www.npmjs.com/package/skills-watcher)
CLI itself lives elsewhere and runs here via `npx`.

## Usage

```sh
npx skills-watcher status          # what drifted since my last review?
npx skills-watcher diff <name>     # review the change
npx skills-watcher save <name>     # I've caught up — then commit the result
```

`status`/`diff` only look. `save` is the "I've caught up" action: it refreshes
the snapshot and stamps the review watermark into `watch.json`. Commit both.

## Automation

`.github/workflows/watch.yml` runs the check daily and opens/updates one
`skills-watcher` issue when an upstream drifts (closing it when you catch up).
It uses the repo's built-in `GITHUB_TOKEN`; set Settings → Actions → Workflow
permissions to read/write so it can manage issues.

## Files

```
watch.json    tracked skills → upstream sources + review watermark
snapshots/    upstream SKILL.md at last review — COMMITTED (the drift baseline)
```
