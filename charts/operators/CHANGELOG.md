# Changelog

All notable changes to this chart will be documented in this file.

The format is based on "Keep a Changelog" and this project adheres to
Semantic Versioning.

For more information, see https://keepachangelog.com/en/1.0.0/

## [Unreleased]

- No changes documented yet.

## [0.1.0] - 2025-10-16

### Added

- Initial release of the `operators` Helm chart (version 0.1.0).
- Templates to deploy Operator OLM resources:
  - `Namespace` (optional, when operator entry defines `namespace`)
  - `OperatorGroup` for operator namespaces
  - `Subscription` to install operators from a catalog
- Values documented for `render`, `operators` map and `argocd` labeling behavior.

### Changed

- N/A

### Fixed

- N/A

<!--
Usage notes:
- Add new changes under "Unreleased".
- When cutting a release, move Unreleased entries into a new section with the released version and date, and update `Chart.yaml` `version` accordingly.
-->
