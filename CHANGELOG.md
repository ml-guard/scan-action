# Changelog

All notable changes to the `ml-guard/scan-action` are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this Action follows the major-version line of the underlying
[`ml-guard`](https://github.com/ml-guard/ml-guard) Python package.

## [1.0.0] - 2026-05-10

### Added
- First public release. Wraps `ml-guard >=0.1, <1.0` with a stable
  composite-action interface.
- Inputs: `path`, `fail-on`, `format`, `output`, `config`, `scanners`,
  `exclude`, `install`, `version`.
- Outputs: `report-path`, `exit-code`.
- Built-in support for `text`, `json`, and `sarif` formats. SARIF output
  uploads cleanly into GitHub Code Scanning via
  `github/codeql-action/upload-sarif`.

[1.0.0]: https://github.com/ml-guard/scan-action/releases/tag/v1.0.0
