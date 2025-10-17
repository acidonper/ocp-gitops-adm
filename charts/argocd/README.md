# Jump App ArgoCD Local Chart

## Introduction

*Jump App ArgoCD Local* chart deploys Jump App's argocd objects in order to deploy Jump App and CI/CD objects. The following objects will be managed by this chart:

- Projects

## Chart configuration (values)

This chart renders Argo CD AppProject objects using values from `values.yaml`. The chart currently provides the `templates/project.yaml` template which iterates the `projects` map and creates `AppProject` resources.

- `projects` (map) — AppProject definitions keyed by project name.
	- `sources` (list) — repository URLs allowed for the project (populates `sourceRepos`).
	- `destinationsNamespaces` (list) — namespaces the project can deploy into (populates `destinations`).
    - `groupsAdmins` (list) - groups who can admin the Argo Project
    - `usersAdmins` (list) - users who can admin the Argo Project

- `applications` (map) — reserved for future use / optional application manifests (not rendered by default by this chart).

- `argocdNamespace` (string) — namespace where AppProject resources are created (defaults to `openshift-gitops` in the template).


## Install

- To render and apply AppProject objects to the cluster:

```bash
helm template . -f values.yaml | oc apply -f -
```

## Local Tests

- Lint

```bash
helm lint
```

- Render templates Locally

```bash
helm template . --debug -f values-test.yaml
```

## Recent changes

See `CHANGELOG.md` for recent updates and release notes. The README was expanded to document chart values and usage (projects, argocdNamespace and template behavior).

## Author Information

Asier Cidon @RedHat