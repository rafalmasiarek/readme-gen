# READMEgen maintenance skill

This repository maintains READMEgen: a gomplate-based README generator.

The main contract of this repository is:

```text
README.md.template
```

The generated README must be valid, readable Markdown with stable whitespace.

## Core rules

When modifying this repository, preserve the existing public contract unless explicitly asked otherwise.

Existing consumers may depend on:

- `README.json`
- `README.yaml`
- `README.yml`
- `README.md.template`
- `bin/readmegen`
- remote template usage through `curl | gomplate`
- `includes`
- `authors`
- `badges`
- `context`
- `references`
- `deprecated.notice`
- license values:
  - `APACHE2`
  - `Apache-2.0`
  - `MIT`
  - `ISC`
  - `GPLv3`
  - `GPL-3.0`

Do not remove or rename existing configuration fields without adding a backwards-compatible migration path.

## Whitespace rules

Whitespace in `README.md.template` is critical.

The template should aggressively trim gomplate control-flow whitespace while keeping normal Markdown spacing.

Good output:

```markdown
# Project

[![Badge](badge.svg)](https://example.com)

## About

Text.

## License

...
```

Bad output:

```markdown
# Project





[![Badge](badge.svg)](https://example.com)









## About
```

Bad output:

```markdown
# Project [![Badge](badge.svg)](https://example.com) ## About Text.
```

Both excessive blank lines and fully collapsed one-line Markdown are bugs.

## Gomplate whitespace guidance

Use `{{-` and `-}}` intentionally.

Prefer trimming around control structures:

```gotemplate
{{- if and (has $config "badges") $config.badges }}
{{ range $badge := $config.badges -}}
[![{{ $badge.name }}]({{ $badge.image }})]({{ $badge.url }})
{{ end -}}
{{- end }}
```

Avoid untrimmed nested control flow like this:

```gotemplate
{{ if has $config "badges" }}
{{ if $config.badges }}
{{ range $badge := $config.badges }}
...
{{ end }}
{{ end }}
{{ end }}
```

That pattern usually creates many blank lines in the generated README.

Also avoid trimming so aggressively that Markdown sections become joined together on one line.

## Required validation after template changes

After changing `README.md.template`, always render a README locally and inspect the result.

Use:

```bash
gomplate \
  -d config=./README.yml \
  -f ./README.md.template \
  -o /tmp/READMEgen.README.md
```

Or, with the CLI:

```bash
./bin/readmegen \
  --config README.yml \
  --template README.md.template \
  --output /tmp/READMEgen.README.md
```

Then inspect the first part:

```bash
sed -n '1,120p' /tmp/READMEgen.README.md
```

Check for:

- no excessive blank lines,
- no collapsed Markdown sections,
- badges directly below the title,
- headings separated by a single blank line,
- license block rendered correctly,
- authors rendered correctly,
- includes appended correctly,
- references rendered at the end.

## Automated whitespace check

When possible, run this check after generating README:

```bash
awk '
  BEGIN { blank=0; fail=0 }
  /^$/ { blank++; if (blank > 2) fail=1; next }
  { blank=0 }
  END {
    if (fail) {
      print "README contains more than two consecutive blank lines"
      exit 1
    }
  }
' /tmp/READMEgen.README.md
```

The generated README should not contain more than two consecutive blank lines.

## Recommended snapshot test

If adding tests, create fixtures like:

```text
testdata/basic/README.yml
testdata/basic/expected.md
```

Then render and compare:

```bash
gomplate \
  -d config=./testdata/basic/README.yml \
  -f ./README.md.template \
  -o /tmp/readmegen-basic.md

diff -u ./testdata/basic/expected.md /tmp/readmegen-basic.md
```

A template change should intentionally update the expected output.

## READMEgen CLI rules

The CLI helper lives at:

```text
bin/readmegen
```

It should remain a small Bash wrapper around `curl` and `gomplate`.

It should support:

```bash
readmegen
readmegen --config README.yaml
readmegen --config README.json --output README.md
readmegen --version v1.0.0
readmegen --template ./README.md.template
readmegen --template-url https://raw.githubusercontent.com/rafalmasiarek/readme-gen/main/README.md.template
readmegen --check
```

Config auto-detection order must remain:

```text
README.yaml
README.yml
README.json
```

Do not rename `--version` unless explicitly asked. In this project, `--version` means READMEgen template git ref, branch, or tag.

## Release model

The main READMEgen template release uses normal tags:

```text
v1.0.0
v1.1.0
```

The CLI helper uses prefixed tags:

```text
cli-v1.0.0
cli-v1.1.0
```

Do not replace template release references with `template-v*` unless explicitly requested.

Correct template usage:

```bash
readmegen --version v1.0.0
```

Correct CLI release:

```text
cli-v1.1.0
```

## Badge rules

This repository has both template releases and CLI releases.

Do not use an unfiltered GitHub release badge if the intention is to show the main template release.

Use filtered badges:

```yaml
badges:
  - name: READMEgen
    image: https://img.shields.io/github/v/release/rafalmasiarek/readme-gen?filter=!cli-*&sort=semver&label=template
    url: https://github.com/rafalmasiarek/readme-gen/releases

  - name: CLI
    image: https://img.shields.io/github/v/release/rafalmasiarek/readme-gen?filter=cli-*&sort=semver&label=cli
    url: https://github.com/rafalmasiarek/readme-gen/releases
```

## Documentation rules

Documentation files in `docs/` should be readable Markdown.

Prefer concise docs:

```text
docs/usage.md
docs/requirements.md
docs/vars.md
docs/workflow-example.md
```

Avoid generating a huge root `README.md` by including every documentation file unless explicitly requested.

For this repository, prefer a short root README with links to documentation.

## Includes

`includes` appends raw Markdown files.

Example:

```yaml
includes:
  - docs/terraform.md
  - docs/contributors.md
```

Use `file.Read` for includes unless there is a clear reason to use gomplate datasource includes.

## all-contributors

Do not implement custom all-contributors logic inside READMEgen unless explicitly requested.

If needed, use it as an optional include:

```yaml
includes:
  - docs/contributors.md
```

The all-contributors tool should manage `docs/contributors.md` separately.

## Deprecation

Preserve support for:

```yaml
deprecated:
  notice: |
    Custom notice.
```

Do not silently remove `deprecated.notice`.

Avoid changing the deprecated model to boolean-only.

## License section

The template should render short license text and preserve `Source:` URLs.

Source URLs should remain configurable near the top of the template:

```gotemplate
{{- $apache2SourceURL := "https://opensource.org/license/apache-2-0/" -}}
{{- $mitSourceURL := "https://opensource.org/licenses/MIT" -}}
{{- $iscSourceURL := "https://opensource.org/licenses/ISC" -}}
{{- $gpl3SourceURL := "https://opensource.org/license/gpl-3-0/" -}}
```

The generated README should link to only one license file:

1. `LICENSE.md`
2. `LICENSE`

Use the first one found.

## Before finalizing changes

Before finalizing a change, run:

```bash
./bin/readmegen \
  --config README.yml \
  --template README.md.template \
  --output /tmp/READMEgen.README.md
```

Then check:

```bash
sed -n '1,160p' /tmp/READMEgen.README.md
```

And verify:

```bash
awk '
  BEGIN { blank=0; fail=0 }
  /^$/ { blank++; if (blank > 2) fail=1; next }
  { blank=0 }
  END {
    if (fail) {
      print "README contains more than two consecutive blank lines"
      exit 1
    }
  }
' /tmp/READMEgen.README.md
```

If the output has excessive blank lines or collapsed Markdown, fix the template before returning the result.
