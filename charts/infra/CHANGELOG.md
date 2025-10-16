# Changelog

All notable changes to this project will be documented in this file.

The format is based on "Keep a Changelog" and this project adheres to
Semantic Versioning.

For more information, see https://keepachangelog.com/en/1.0.0/

## [Unreleased]

- No changes documented yet.

## [0.1.1] - 2025-10-16

### Added

- Initial release of the infra Helm chart (`infra-chart` version 0.1.1).
- Provides Kubernetes/OpenShift manifests and templates for:
  - ClusterRoleBinding for cluster users and groups
  - Namespace creation
  - RoleBindings for namespace admin groups and users

### Changed

- N/A

### Fixed

- N/A


<!--
How to use:
- Add new unreleased changes under the "Unreleased" section.
- When cutting a release, move the Unreleased entries into a new section
  with the released version and date, and update the chart's `Chart.yaml`
  `version` field accordingly.
-->
