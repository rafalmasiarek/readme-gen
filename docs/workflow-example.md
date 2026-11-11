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
