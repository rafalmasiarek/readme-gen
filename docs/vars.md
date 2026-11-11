## Configuration

READMEgen reads configuration from one of the following files:

1. `README.yaml`
2. `README.yml`
3. `README.json`

When using `gomplate` directly, pass the selected file as the `config` datasource:

```bash
gomplate -d config=./README.yaml -f README.md.template -o README.md
```

When using the `readmegen` CLI, the config file is detected automatically unless `--config` is provided.

## Variables

| Name | Type | Default | Description |
| --- | --- | --- | --- |
| `name` | string | `""` | Main repository or project name displayed as the README title. |
| `repo_url` | string | `""` | Repository URL. Used to generate the predefined `[repo_link]` reference. |
| `license` | string | `""` | License identifier. Supported values: `APACHE2`, `Apache-2.0`, `MIT`, `ISC`, `GPLv3`, `GPL-3.0`, `BUSL-1.1`. |
| `license_change_date` | string | unset | BUSL-1.1 only. Date the license converts to the Change License, rendered in the license notice. Must be quoted in YAML to avoid timestamp auto-parsing. |
| `license_contact` | string | unset | BUSL-1.1 only. Contact shown for alternative/commercial licensing inquiries. |
| `logo` | string | `""` | Optional logo URL or path displayed below the title and badges. |
| `badges` | array | `[]` | List of badges displayed below the title. |
| `context` | array | `[]` | List of markdown blocks rendered before the license section. |
| `authors` | array | `[]` | List of project authors or maintainers. |
| `includes` | array | `[]` | List of raw markdown files to append to the README. |
| `references` | array | `[]` | List of reference links generated as `[1]`, `[2]`, etc. |
| `deprecated` | object | unset | Marks the repository as deprecated. Can contain a custom `notice`. |
| `icons_base_url` | string | `https://cdn.masiarek.pl/icons` | Base URL used to resolve `{icon name}` references. |
| `icons_version` | string | `v2.0.3` | Version path segment used to resolve `{icon name}` references. |

## License values

Supported license values:

```text
APACHE2
Apache-2.0
MIT
ISC
GPLv3
GPL-3.0
BUSL-1.1
```

The template renders a short license notice, keeps the `Source:` URL, and links to the first existing license file in this order:

1. `LICENSE.md`
2. `LICENSE`

### BUSL-1.1

`BUSL-1.1` renders a shield badge and a short notice instead of the `Source:` URL pattern used by the other licenses. Two optional keys extend it:

```yaml
license: BUSL-1.1
license_change_date: "2029-01-01"
license_contact: https://example.com/contact/
```

- `license_change_date`, when set, adds a line stating commercial use is restricted until that date. Quote the value in YAML (`"2029-01-01"`) — an unquoted date is parsed as a YAML timestamp and renders with a time component instead of the plain date.
- `license_contact`, when set, adds a line pointing to an alternative-licensing contact.

Both lines are optional and independent of each other. The same `LICENSE`/`LICENSE.md` link resolution above still applies.

## Badges

Example:

```yaml
badges:
  - name: Build
    image: https://github.com/example/project/actions/workflows/test.yml/badge.svg
    url: https://github.com/example/project/actions/workflows/test.yml
```

Without a URL:

```yaml
badges:
  - name: Status
    image: https://img.shields.io/badge/status-active-brightgreen
```

## Authors

Example:

```yaml
authors:
  - name: Rafał Masiarek
    email: rafal@example.com
    github: rafalmasiarek
    website: https://example.com
```

Rendered as:

```markdown
- Rafał Masiarek | [website](https://example.com) | [email](mailto:rafal@example.com) | [github](https://github.com/rafalmasiarek)
```

## Context

`context` is an array of markdown blocks rendered before the license section.

Example:

```yaml
context:
  - |
    ## About

    This project standardizes README generation across repositories.

  - |
    ## Features

    - Markdown template rendering
    - JSON and YAML config support
    - Optional includes
```

## Includes

`includes` appends raw markdown files to the generated README.

Example:

```yaml
includes:
  - docs/terraform.md
  - docs/contributors.md
```

This is useful for generated documentation from other tools, such as Terraform docs or contributors lists.

## References

`references` generates numbered markdown references.

Example:

```yaml
references:
  - https://cloudposse.com
  - https://github.com/cloudposse/build-harness
```

Rendered as:

```markdown
[1]: https://cloudposse.com
[2]: https://github.com/cloudposse/build-harness
```

The following reference is always generated:

```markdown
[repo_link]: <repo_url>
```

## Icons

Write `{icon name}` anywhere inside a `context` string or an `includes` file to render an icon image. The syntax is exact: `{icon`, one space, the name, `}` — no extra spaces are recognized.

```yaml
context:
  - |
    ## {icon rocket} About

    This module provisions {icon aws} resources.
```

Renders as:

```markdown
## ![rocket](https://cdn.masiarek.pl/icons/v2.0.3/rocket.svg) About

This module provisions ![aws](https://cdn.masiarek.pl/icons/v2.0.3/aws.svg) resources.
```

`name` maps directly to a file name at `{icons_base_url}/{icons_version}/{name}.svg`. Override the source per repository:

```yaml
icons_base_url: https://cdn.masiarek.pl/icons
icons_version: v2.0.3
```

## Banners

Banners are a shared library bundled with `readme-gen` itself, not something a consumer repository configures. Every image file in this repository's own `banners/` directory is rendered, unconditionally, right after `logo`, in every generated README — there is no config key and no per-repo opt-in/opt-out.

The `readmegen` CLI resolves the list via the GitHub Contents API at the pinned `READMEGEN_VERSION` (the `--version`/`-v` flag, default `main`), so which banners appear is tied to which template version a repository is pinned to — the same way the rest of the template content is. Adding or removing a file under `banners/` in this repository is a content change to the template and gets its own tag (see `auto-tag-template.yml`).

This only works through the `readmegen` CLI (it resolves the list and passes it to the template as a `banners` datasource) — invoking `gomplate` directly against `README.md.template` without also passing `-d banners=...` renders no banners.

## Deprecated repositories

Default deprecation notice:

```yaml
deprecated: {}
```

Custom deprecation notice:

```yaml
deprecated:
  notice: |
    ### **THIS REPOSITORY IS NO LONGER MAINTAINED**

    This project has been replaced by another repository.
```

## Full YAML example

```yaml
name: READMEgen
repo_url: https://github.com/rafalmasiarek/readme-gen
license: GPL-3.0

badges:
  - name: Release
    image: https://img.shields.io/github/v/release/rafalmasiarek/readme-gen
    url: https://github.com/rafalmasiarek/readme-gen/releases

context:
  - |
    ## About

    READMEgen is a simple templating method for standardizing README files.

includes:
  - docs/usage.md
  - docs/requirements.md

authors:
  - name: Rafał Masiarek
    github: rafalmasiarek
    website: https://masiarek.pl

references:
  - https://gomplate.ca
```

## Full JSON example

```json
{
  "name": "READMEgen",
  "repo_url": "https://github.com/rafalmasiarek/readme-gen",
  "license": "GPL-3.0",
  "badges": [
    {
      "name": "Release",
      "image": "https://img.shields.io/github/v/release/rafalmasiarek/readme-gen",
      "url": "https://github.com/rafalmasiarek/readme-gen/releases"
    }
  ],
  "context": [
    "## About\n\nREADMEgen is a simple templating method for standardizing README files."
  ],
  "includes": [
    "docs/usage.md",
    "docs/requirements.md"
  ],
  "authors": [
    {
      "name": "Rafał Masiarek",
      "github": "rafalmasiarek",
      "website": "https://masiarek.pl"
    }
  ],
  "references": [
    "https://gomplate.ca"
  ]
}
```
