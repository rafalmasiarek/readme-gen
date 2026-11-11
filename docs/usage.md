## Usage

READMEgen can be used directly with `gomplate` or through the `readmegen` CLI helper.

## Using gomplate directly

Generate `README.md` from a JSON config:

```bash
curl -fsSL https://raw.githubusercontent.com/rafalmasiarek/readme-gen/main/README.md.template \
  | gomplate -d config=./README.json > README.md
```

Generate `README.md` from a YAML config:

```bash
curl -fsSL https://raw.githubusercontent.com/rafalmasiarek/readme-gen/main/README.md.template \
  | gomplate -d config=./README.yaml > README.md
```

Use a pinned template release:

```bash
curl -fsSL https://raw.githubusercontent.com/rafalmasiarek/readme-gen/v1.0.0/README.md.template \
  | gomplate -d config=./README.yaml > README.md
```

Use a local template file:

```bash
gomplate \
  -d config=./README.yaml \
  -f ./README.md.template \
  -o README.md
```

## Using readmegen CLI

Generate `README.md` from the first existing config file:

1. `README.yaml`
2. `README.yml`
3. `README.json`

```bash
readmegen
```

Use a specific config file:

```bash
readmegen --config README.json
```

Use a specific output file:

```bash
readmegen --output docs/README.md
```

Use a pinned template release:

```bash
readmegen --version v1.0.0
```

Use a local template file:

```bash
readmegen --template ./README.md.template
```

Use a custom remote template URL:

```bash
readmegen --template-url https://raw.githubusercontent.com/rafalmasiarek/readme-gen/main/README.md.template
```

Check whether the generated README is up to date:

```bash
readmegen --check
```

## Example Makefile

```makefile
READMEGEN_VERSION ?= main
READMEGEN_CONFIG ?=
READMEGEN_OUTPUT ?= README.md

.PHONY: readme
readme:
	readmegen \
		$(if $(READMEGEN_CONFIG),--config $(READMEGEN_CONFIG),) \
		--output $(READMEGEN_OUTPUT) \
		--version $(READMEGEN_VERSION)

.PHONY: check-readme
check-readme:
	readmegen \
		$(if $(READMEGEN_CONFIG),--config $(READMEGEN_CONFIG),) \
		--output $(READMEGEN_OUTPUT) \
		--version $(READMEGEN_VERSION) \
		--check
```

Examples:

```bash
make readme
make readme READMEGEN_CONFIG=README.yaml
make readme READMEGEN_VERSION=v1.0.0
make check-readme
```
