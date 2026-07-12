# skills tracker

Some of my skills started as clean-room rewrites of a popular upstream skill. I
wrote the code myself, but the idea came from someone else's work, and that
upstream keeps changing. This repo watches those upstreams so I notice when they
change and can decide whether to fold the change into my version.

It holds two things: my `watch.json` (the list of what I track) and the
committed `snapshots/` baseline (each upstream's `SKILL.md` as it looked at my
last review). The [skills-watcher](https://www.npmjs.com/package/skills-watcher)
CLI does the actual work and lives elsewhere. Here it runs through `npx`, so
there is nothing to install.

## The idea

Three terms carry the whole thing:

- **Drift** is the gap between what an upstream `SKILL.md` looks like now and the
  snapshot I last reviewed.
- **Snapshot** is that reviewed copy, committed under `snapshots/`. It is the
  baseline every future check compares against.
- **Watermark** is the `last_reviewed_sha` and `last_reviewed_at` in
  `watch.json`. It records the exact upstream commit I signed off on.

A skill has drifted when the upstream moved past my watermark. Nothing here
touches my skills or the upstream repos. It only tells me when to go look.

## Usage

```sh
npx skills-watcher status          # what drifted since my last review?
npx skills-watcher diff <name>     # review the change
npx skills-watcher save <name>     # I've caught up, then commit the result
```

`status` and `diff` only read. `save` is the "I've caught up" action: it
refreshes the snapshot and stamps the review watermark into `watch.json`. Commit
both files after a `save` so the new baseline sticks.

## Tracking a new skill

Add an entry to `watch.json` keyed by the local skill name, then run
`npx skills-watcher save <name>` once to write the first snapshot and watermark.

```json
{
  "grilling": {
    "sources": [
      {
        "repo": "mattpocock/skills",
        "path": "skills/productivity/grilling/SKILL.md",
        "branch": "main",
        "last_reviewed_sha": "e5932a7a47e5cae312c1b814ce6194b09aa27be1",
        "last_reviewed_at": "2026-07-12"
      }
    ]
  }
}
```

A skill can list more than one source under `sources`. Each source becomes its
own snapshot file, numbered by position (`grilling__0.md`, `grilling__1.md`, and
so on).

## Automation

`.github/workflows/watch.yml` runs the check once a day at 06:17 UTC and can
also be triggered by hand from the Actions tab. When an upstream drifts it opens
or updates one `skills-watcher` issue, and it closes that issue once I catch up.
The job itself stays green; the issue is the signal.

It runs on the repo's built-in `GITHUB_TOKEN`, so no extra secrets are needed.
The workflow already sets `issues: write`, but if issue creation fails, check
Settings, Actions, Workflow permissions and set it to read and write.

## Files

```
watch.json    tracked skills, their upstream sources, and the review watermark
snapshots/    upstream SKILL.md at last review, committed as the drift baseline
```

`snapshots/` is deliberately not gitignored. The scheduled check needs the
committed baseline to tell what changed.
