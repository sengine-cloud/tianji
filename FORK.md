# fork/1.33.9-patched

Deployment branch. Upstream `v1.33.9` plus local fixes that are still in review
upstream, built into `ghcr.io/sengine-cloud/tianji` by
`.github/workflows/fork-image.yml` and run by the `tianji` Deployment on
spot-lon-infinity (stats.sengine.cloud).

## Branch contract

- Every fix here is a cherry-pick (`-x`) of a commit that also exists on a
  `fix/*` branch cut from `upstream/master`. The `fix/*` branch is what the
  upstream PR sees. Write the fix there first, cherry-pick second.
- `.github/workflows/fork-image.yml` and this file are fork-only. They must
  never end up in a PR against `msgbyte/tianji`.
- Don't push `v*` tags to `origin`, and don't push to the fork's `master`:
  upstream's `docker-publish.yml`, `ci.yaml` and `ci-docker.yaml` fire on those.

## What's carried

| Commit | Upstream PR |
| --- | --- |
| fix(website): bind filter values in website and telemetry queries | [#295](https://github.com/msgbyte/tianji/pull/295) |
| fix(website): pass language, event and utm filters through | [#295](https://github.com/msgbyte/tianji/pull/295) |

Why: every website filter (country, url, os, utm_*, ...) on the stats,
pageviews and metrics endpoints matched zero rows, which broke filtering in
tianji-mcp (sengine-cloud/tianji-mcp#6, #7).

## Publishing an image

Push to the branch, or run the workflow manually. It publishes:

```
ghcr.io/sengine-cloud/tianji:1.33.9-fork.<run number>
ghcr.io/sengine-cloud/tianji:sha-<short sha>
```

Both immutable. There is no `latest`. The run number counts per workflow, not
per branch, so the first build on a rebased branch does not start at `.1`.

## Rebasing onto a new upstream release

Cut `fork/<version>-patched` from the new tag, cherry-pick the fixes that are
not in it yet, then this build commit, and bump `UPSTREAM_VERSION` in the
workflow and the version strings in this file.

## Retiring it

When #295 lands in a release, point the Deployment back at
`moonrailgun/tianji:<that version>` and restore the Flux image automation
(see `clusters/spot-lon-infinity/image-automation/tianji.yaml` in
fluxcd-infra).
