# READMEgen
[![README.md.template](https://img.shields.io/github/v/release/rafalmasiarek/readme-gen?filter=!cli-*&sort=semver&label=template)](https://github.com/rafalmasiarek/readme-gen/releases)
[![readmegen-cli](https://img.shields.io/github/v/release/rafalmasiarek/readme-gen?filter=cli-*&sort=semver&label=cli)](https://github.com/rafalmasiarek/readme-gen/releases)

![READMEgen](assets/readmegen-logo.png)
## About

READMEgen is a small README generation standard based on [gomplate][1].

It provides a reusable `README.md.template`, a simple `readmegen` CLI helper, and a GitHub Actions workflow for keeping generated README files up to date across repositories.
Idea was *taken* from [cloudposse][3] and them [build-harness][4].


## Features

- Standardized README generation
- JSON and YAML configuration support
- Optional markdown includes
- Built-in license sections
- Local and remote template support
- CLI helper for local usage, hooks, and CI
- Reusable GitHub Actions workflow for scheduled README regeneration


## Documentation

- [Usage](docs/usage.md)
- [Requirements](docs/requirements.md)
- [Configuration](docs/vars.md)
- [GitHub Actions workflow example](docs/workflow-example.md)



## License
[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)

```text
GNU GENERAL PUBLIC LICENSE
Version 3, 29 June 2007

This program is free software: you can redistribute it and/or modify it
under the terms of the GNU General Public License as published by the
Free Software Foundation, either version 3 of the License, or at your option
any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY.

Source: https://opensource.org/license/gpl-3-0/
```
## Authors

- Rafał Masiarek | [website](https://masiarek.pl) | [email](mailto:rafal@masiarek.pl) | [github](https://github.com/rafalmasiarek)


<!-- references -->

[repo_link]: https://github.com/rafalmasiarek/readme-gen
[1]: https://gomplate.ca
[2]: https://github.com/hairyhenderson/gomplate
[3]: https://cloudposse.com
[4]: https://github.com/cloudposse/build-harness
[5]: https://github.com/rafalmasiarek/readme-gen/releases

