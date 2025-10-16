# Infrastructure Chart

This Helm Chart includes a set of templates to setting up an Openshift cluster generating the following elements:

* Namespaces
* RoleBinding to assign admins (users or groups) per namespace
* ClusterRoleBinding to assign cluster-admins

## Chart configuration (values)

- `namespaces` (map) - default: `{}`
	- A map where each key is a namespace name and the value configures namespace-scoped settings.
	- Shape:

		namespaces:
			<namespace-name>:
				admins:
					users: []   # list of user names
					groups: []  # list of group names

	- Example:

		namespaces:
			team-a:
				admins:
					users:
						- alice@example.com
					groups:
						- team-a-admins

	- What the chart generates: a `Namespace` resource for each key in this map, and `RoleBinding` objects in that namespace for every entry under `admins.users` and `admins.groups` (granting the `admin` role).

- `rbac.cluster.admins` (map) - default: `groups: []`, `users: []`
	- Controls cluster-level administrators created as `ClusterRoleBinding` objects.
	- Shape:

		rbac:
			cluster:
				admins:
					users: []   # list of user names to bind to cluster-admin
					groups: []  # list of groups to bind to cluster-admin

	- Example:

		rbac:
			cluster:
				admins:
					users:
						- admin-user@example.com
					groups:
						- cluster-admins

- `argocd` (map)
	- `enabled` (bool) - default: `true` — when true, a label is added to created Namespaces to mark them as managed by Argo CD.
	- `controller` (string) - default: `openshift-gitops` — the value used for the `argocd.argoproj.io/managed-by` label.

	- Example:

		argocd:
			enabled: true
			controller: openshift-gitops

How the values are applied
- Namespaces: the chart iterates over `.Values.namespaces` to render `Namespace` manifests. If `argocd.enabled` is true, each namespace will include the label `argocd.argoproj.io/managed-by: <controller>`.
- Namespace admin bindings: for each namespace value, the chart renders `RoleBinding` resources named `admins-ns-<user|group>` binding the `admin` ClusterRole to listed users and groups.
- Cluster admin bindings: the chart renders `ClusterRoleBinding` resources named `clusteradmin-<user|group>` for each entry under `.Values.rbac.cluster.admins`.

## Testing

The file _values-test.yaml_ is key to test all the functionality implement in the chart. By default, the file _value.yaml_ is empty in order to not generate files that could not be necessary in all the Openshift Clusters for every environment.

Please execute the following command in the CI process to test the chart functionality:

```$bash
helm template . -f values-test.yaml
```

Tips
- Keep sensitive principals (users) under environment-specific values files and avoid committing secrets to git.
- When releasing a new chart version, update `charts/infra/Chart.yaml` and `charts/infra/CHANGELOG.md` accordingly.

## Author

Asier Cidon @RedHat