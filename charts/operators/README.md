# Openshift Operators chart

This Helm chart installs Operator Lifecycle Manager (OLM) resources in OpenShift: Subscriptions, OperatorGroups and optional Namespaces required by operators.

Generated resources
- Namespace (one per operator if the operator entry defines `namespace`)
- OperatorGroup (created per operator namespace)
- Subscription (to install the operator from a catalog source)

## Quick start

Install the chart into the current cluster (chart will render only if `.Values.render` is true):

```bash
helm install operators .
```

Render templates and apply to the cluster (useful in CI):

```bash
helm template . --debug | oc apply -f -
```

## Chart values (summary)

Defaults are defined in `values.yaml`. Important keys:

- `render` (bool, default: `false`)
	- When `false` the chart renders nothing. Set to `true` to enable rendering in environments where you want operator resources created.

- `operators` (map)
	- A map of operator identifiers to configuration. Each operator entry can contain:
		- `enabled` (bool) - enable rendering of resources for this operator
		- `name` (string) - subscription name / operator name
		- `channel` (string) - subscription channel (eg. `stable-2.6`)
		- `approval` (string) - `Automatic` or `Manual` installPlanApproval
		- `namespace` (string) - the namespace where the operator and resources should be created
		- `sourcenamespace` (string) - catalog source namespace (defaults to `openshift-marketplace` in the template)
		- `source` (string) - catalog source name (defaults to `redhat-operators` in the template)
		- `csv` (string, optional) - startingCSV to pin a specific operator CSV

- `argocd` (map)
	- `enabled` (bool, default: `true`) — if true, the chart will add the label `argocd.argoproj.io/managed-by: <controller>` to created namespaces.
	- `controller` (string, default: `openshift-gitops`) — the value used for the Argo CD managed-by label.

How templates use values?

- If `.Values.render` is false the chart will not render any templates.
- Namespaces: for each operator with `namespace` set and `enabled: true`, the chart renders a `Namespace` with monitoring and (optionally) the Argo CD label.
- OperatorGroup: created in the operator namespace (name equals operator `name`) and targets the single operator namespace.
- Subscription: created in the operator namespace (or `openshift-operators` when `namespace` is not provided) with `.spec.channel`, `.spec.installPlanApproval` and optional `startingCSV` when `csv` is provided.

## Testing and linting

- Lint the chart:

```bash
helm lint
```

- Dry-run install (Helm client-side):

```bash
helm install operators . --dry-run --debug
```

- Render templates to inspect output:

```bash
helm template . --debug -f values-test.yaml
```

## Good practices and troubleshooting

- Keep `render` explicitly set per environment. For CI or environment overlays set `render: true` in the environment values file (for example `values/cluster-*/values-operators.yaml`) and keep `false` in generic values when you don't want operators installed.
- Pin `csv` or `channel` where stability is required. Using `startingCSV` helps reproduce exact operator versions.
- If a subscription fails to install, check the OperatorHub/catalog source (`oc get packagemanifests` and `oc get catalogsources -n openshift-marketplace`) and review the Subscription and InstallPlan objects for errors.
- Argo CD: if you deploy this chart via Argo CD, keep in mind the chart's Namespace manifests may be subject to Argo CD namespace-scoping and permissions; using the root Application with `CreateNamespace=true` can help.

## Author

Asier Cidon @RedHat
