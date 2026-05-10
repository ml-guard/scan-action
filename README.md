# ML Guard Security Scan

> GitHub Action wrapper around [ml-guard](https://github.com/ml-guard/ml-guard).

Scans your repository for ML supply-chain risks: malicious pickle code,
embedded executables in safetensors, suspicious ONNX operators, leaked
API keys, vulnerable PyPI dependencies, malicious packages.

Drops findings into the **Security → Code scanning** tab via SARIF.

## Usage

Minimal:

```yaml
- uses: ml-guard/scan-action@v1
  with:
    path: ./models
```

Full integration with GitHub Code Scanning:

```yaml
name: ML Security Scan
on:
  push:
    branches: [main]
  pull_request:

permissions:
  contents: read
  security-events: write

jobs:
  ml-guard:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: ml-guard/scan-action@v1
        with:
          path: ./models
          fail-on: critical
          format: sarif
          output: ml-guard.sarif
          exclude: 'tests/fixtures/**'

      - if: always()
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: ml-guard.sarif
```

## Inputs

| Name        | Default            | Description                                                |
| ----------- | ------------------ | ---------------------------------------------------------- |
| `path`      | `.`                | File or directory to scan                                  |
| `fail-on`   | `critical`         | Fail the build on findings ≥ this severity                 |
| `format`    | `sarif`            | Output format: `text` / `json` / `sarif`                   |
| `output`    | `ml-guard.sarif`   | Path for the report file (omit for stdout)                 |
| `config`    | (none)             | Path to a `.ml-guard.yml` configuration file               |
| `scanners`  | (all)              | Comma-separated list of scanner names                      |
| `exclude`   | (none)             | Comma-separated globs to skip                              |
| `install`   | `true`             | Whether to `pip install mlsupplychain` (set `false` if pre-installed) |
| `version`   | (latest)           | Specific `ml-guard` version to install                     |

## Outputs

| Name          | Description                                       |
| ------------- | ------------------------------------------------- |
| `report-path` | Path to the generated report                      |
| `exit-code`   | `0` = clean, `1` = findings ≥ `fail-on`           |

## What ml-guard catches

- **Pickle RCE** — dangerous `__reduce__` payloads, both protocol ≤ 3
  and protocol ≥ 4 (`STACK_GLOBAL`)
- **Safetensors integrity** — malformed headers, lying offsets, ELF/MZ
  signatures hidden after the last tensor
- **ONNX threats** — custom-domain operators, `external_data`
  pointing at `/etc/...` or `https://attacker.tld/...`
- **Leaked credentials** — AWS, GitHub, OpenAI, Anthropic, Hugging Face,
  Google, Slack, Stripe, JWT, PEM private keys
- **Vulnerable dependencies** — cross-checks pinned versions in
  `requirements.txt`, `Pipfile.lock`, `pyproject.toml`, `environment.yml`
  against the OSV database

See <https://github.com/ml-guard/ml-guard#readme> for full docs.

## Versioning

We follow the major version of `ml-guard` itself. Pin to `@v1` for
non-breaking patches automatically; pin to `@v1.2.3` to lock to a
specific release.

When `ml-guard` ships a new major version, this Action either advances
its `v2` tag or moves to a new repo (announced in the release notes).

## License

Apache-2.0. See [`LICENSE`](LICENSE).
