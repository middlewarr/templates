# Middlewarr Templates

This repository is a **Middlewarr template source**: a plain git repo of JSON files, each describing exactly which API endpoints a third-party integration needs on Radarr, Sonarr, Lidarr, and/or Prowlarr. Middlewarr uses these templates to build a per-app allowlist, so a proxied integration only ever reaches the endpoints it actually needs.

## Install

In your Middlewarr instance, go to **Settings → Templates** and add this repository's URL and branch. Templates are synced from that branch's `HEAD` — there is no versioning or tag support, so pin a fork/branch if you need stability.

## Repository layout

- `<id>.json` — one file per integration, at the repo root.
- `repository.json` — optional manifest with a human-readable `name` and `description` for this repo. Purely descriptive; never trusted for security or identity.

## Template format

```json
{
  "id": "<filename-without-.json>",
  "name": "Display name of the integration",
  "url": "https://github.com/owner/integration-project",
  "endpoints": {
    "<service-type>": {
      "/api/v3/some/path": ["get", "post"],
      "/api/v3/other/{id}": ["put"]
    }
  }
}
```

- **id** must exactly equal the filename (no `.json`, no path).
- **name** and **endpoints** are required — a template missing either is rejected.
- **`<service-type>`** keys must be one of: `radarr`, `sonarr`, `lidarr`, `prowlarr`.
- Methods must be lowercase HTTP verbs (`get`, `post`, `put`, `delete`, …) and should match that service type's actual OpenAPI spec — an unknown path/method is logged as a warning, not fatal, but should be avoided.
- **url** points to the upstream integration's own homepage/repo — not to this repo.
- List only the endpoints the integration actually needs. This file becomes an allowlist; over-broad access defeats the purpose.

## Contributing a template

1. Add `<id>.json` at the repo root, matching the schema above.
2. Make sure `id` matches the filename exactly.
3. Only include endpoints the integration genuinely calls.
4. Open a PR.
