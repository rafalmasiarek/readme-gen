# GitHub Actions workflow example

Copy this file to:

```text
.github/workflows/readme.yml
```

## Minimal example

This workflow runs manually and every night at 02:00 UTC.

```yaml
name: readme

on:
  workflow_dispatch:

  schedule:
    - cron: "0 2 * * *"

permissions:
  contents: write

jobs:
  generate-readme:
    uses: rafalmasiarek/readme-gen/.github/workflows/generate-readme.yml@main
```

## Pin README template version

Use this when you want to keep the README template fixed to a specific release.

```yaml
name: readme

on:
  workflow_dispatch:

  schedule:
    - cron: "0 2 * * *"

permissions:
  contents: write

jobs:
  generate-readme:
    uses: rafalmasiarek/readme-gen/.github/workflows/generate-readme.yml@main
    with:
      template_version: v1.0.0
```

## Full example

Use this when you want to pin the CLI version, template version, config file, output file, and commit message.

```yaml
name: readme

on:
  workflow_dispatch:

  schedule:
    - cron: "0 2 * * *"

permissions:
  contents: write

jobs:
  generate-readme:
    uses: rafalmasiarek/readme-gen/.github/workflows/generate-readme.yml@main
    with:
      cli_version: cli-v1.1.0
      template_version: v1.0.0
      config: README.yaml
      output: README.md
      commit_message: "chore: regenerate README [skip ci]"
```

## Rebuild only when the template changes

`generate-readme.yml` always regenerates on its trigger (push, schedule, dispatch) and only commits when the *output* changed. `check-template-update.yml` is stricter: it skips all work — no gomplate install, no generation — unless a newer **template** release exists upstream.

It tracks the currently-pinned template release in a small state file (default `.readmegen-template-version`, a single line like `v1.0.0`, committed alongside `README.md`). On each run it compares that file against the latest `v*.*.*` release on `rafalmasiarek/readme-gen` (CLI releases, tagged `cli-v*.*.*`, are ignored). If they match, the job exits after two lightweight steps — no toolchain is installed. If they differ, it generates with `--version <latest>`, updates the state file, and commits both files together.

```yaml
name: readme-template-check

on:
  workflow_dispatch:

  schedule:
    - cron: "0 3 * * 1"

permissions:
  contents: write

jobs:
  check-template-update:
    uses: rafalmasiarek/readme-gen/.github/workflows/check-template-update.yml@main
```

Commit the generated `.readmegen-template-version` file the first time it appears — it is the record of what's currently pinned, the same way `README.md` itself is committed output.

## Using the composite action instead

`generate-readme.yml` is a full job: checkout, install, generate, commit, push. Use `action.yml` instead when you want README generation as a single step inside a job you already control — for example, alongside other steps, or with your own commit logic.

```yaml
name: readme

on:
  push:
    branches: [main]

permissions:
  contents: write

jobs:
  generate-readme:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: rafalmasiarek/readme-gen@main
        with:
          config: README.yaml
          output: README.md

      - name: Commit and push
        run: |
          set -euo pipefail

          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"

          git add README.md
          git diff --cached --quiet || git commit -m "chore: regenerate README [skip ci]"
          git push
```

The action does not commit or push — it only writes `output`. Pin it to a specific template release the same way as any action (`uses: rafalmasiarek/readme-gen@v1.0.0`), or pass `template:` to point at a local template file instead of the one bundled with the action.

When pinning, also pass `version:` with the same ref — the action can't introspect the ref used in its own `uses:` line, so `version:` is how it resolves the `banners/` library consistently with the pinned template:

```yaml
- uses: rafalmasiarek/readme-gen@v1.0.0
  with:
    config: README.yaml
    output: README.md
    version: v1.0.0
```
