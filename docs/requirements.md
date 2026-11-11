## Requirements

READMEgen requires:

- [`gomplate`](https://gomplate.ca)
- `curl`, when using a remote template URL
- `bash`, when using the `readmegen` CLI helper

## Optional tools

The following tools are optional:

- `make`, when using the example `Makefile`
- GitHub Actions, when using the scheduled README generation workflow

## Installation

Install `gomplate` using the method preferred for your system.

Example for Linux:

```bash
GOMPLATE_VERSION="v4.3.3"

curl -fsSL \
  "https://github.com/hairyhenderson/gomplate/releases/download/${GOMPLATE_VERSION}/gomplate_linux-amd64" \
  -o /usr/local/bin/gomplate

chmod +x /usr/local/bin/gomplate

gomplate --version
```

## READMEgen CLI

The `readmegen` CLI helper is optional. It wraps `curl` and `gomplate` and adds:

- automatic config file detection,
- YAML support,
- custom output file support,
- local or remote template support,
- check mode for CI and hooks.

Install the CLI from a release:

```bash
CLI_VERSION="cli-v1.1.0"

curl -fsSLO "https://github.com/rafalmasiarek/readme-gen/releases/download/${CLI_VERSION}/readmegen-cli"
curl -fsSLO "https://github.com/rafalmasiarek/readme-gen/releases/download/${CLI_VERSION}/readmegen-cli.md5"

md5sum -c readmegen-cli.md5

sudo install -m 0755 readmegen-cli /usr/local/bin/readmegen

rm -f readmegen-cli readmegen-cli.md5

readmegen --help
```

A rolling `cli-latest` tag/release is also kept pointing at the most recent CLI build (re-uploaded on every `cli-v*.*.*` release, marked as a GitHub prerelease so it never shows as the repo's "Latest" release). Use a pinned `cli-v*.*.*` tag for anything other than tracking HEAD.
