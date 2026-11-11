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
| `license` | string | `""` | License identifier. Supported values: `APACHE2`, `Apache-2.0`, `MIT`, `ISC`, `GPLv3`, `GPL-3.0`. |
| `logo` | string | `""` | Optional logo URL or path displayed below the title and badges. |
| `badges` | array | `[]` | List of badges displayed below the title. |
| `context` | array | `[]` | List of markdown blocks rendered before the license section. |
| `authors` | array | `[]` | List of project authors or maintainers. |
| `includes` | array | `[]` | List of raw markdown files to append to the README. |
| `references` | array | `[]` | List of reference links generated as `[1]`, `[2]`, etc. |
| `deprecated` | object | unset | Marks the repository as deprecated. Can contain a custom `notice`. |

## License values

Supported license values:

```text
APACHE2
Apache-2.0
MIT
ISC
GPLv3
GPL-3.0
```

The template renders a short license notice, keeps the `Source:` URL, and links to the first existing license file in this order:

1. `LICENSE.md`
2. `LICENSE`

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
