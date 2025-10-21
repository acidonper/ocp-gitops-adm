# External Secrets chart

This Helm chart manages secret stores and ExternalSecret resources using the External Secrets Operator (external-secrets.io) to sync secrets from external providers (for example AWS Secrets Manager) into OpenShift Secrets.

## Generated resources

- `SecretStore` resources (one per entry in `secretStores`)
- `ExternalSecret` resources (one per entry in `externalSecrets`)

## Prerequisites

- Red Hat External Secrets Operator CRDs and controller must be installed in the cluster.

## Chart values

- `secretStores` (map)
  - A map where each key is the SecretStore name and the value defines its configuration. Each entry may include:
    - `namespace` (string) — namespace where the SecretStore CR will be created (defaults to `openshift-operators`).
    - `provider` (object) — provider-specific configuration (for example `aws` block for AWS Secrets Manager). The provider block is rendered directly under `spec.provider`.

- `externalSecrets` (map)
  - A map where each key is the ExternalSecret name and the value defines its configuration. Each entry may include:
    - `namespace` (string) — namespace where the ExternalSecret is created (defaults to `openshift-operators`).
    - `refreshInterval` (string) — refresh interval for the ExternalSecret (defaults to `1m`).
    - `secretStore` (string) — name of the SecretStore to reference (defaults to `aws-secretstore`).
    - `matching` (object) — mapping used by the chart templates to build `target.name` and `data` entries. Example shape:
      - `ocpSecretName`: name of the resulting OpenShift Secret
      - `secrets`: list of objects with `originSecretName` and `ocpSecretKey` fields

## Testing

Render templates:

```bash
helm template . -f values-test.yaml
```

## Install

Render templates and apply to cluster:

```bash
helm template . -f values-test.yaml | oc apply -f -
```

## Lint / Dry-run

```bash
helm lint
helm install secrets . --dry-run --debug
```

## Security and notes

- Keep credentials for external providers (for example AWS access keys) in Kubernetes Secrets and reference them via `secretRef` as shown in the example.
- Validate that the External Secrets Operator supports the provider block structure you use.

## Using Hashicorp Vault secret manager

- Create the respective secret in Vault:

```bash
vault kv put secret/foo my-value=s3cr3t
```

- Create a secret in the final namespace to provide Vault credentials to external secrets:

```bash
oc create secret generic vault-token --from-literal=password=xxxx -n openshift-gitops
```


## Using AWS secret management

- Create the respective secrets in AWS:

```bash
aws secretsmanager create-secret --name openshift-mysecret01 --secret-string securedinformation --region us-east-2
aws secretsmanager create-secret --name openshift-mysecret02 --secret-string securedinformation2 --region us-east-2
aws secretsmanager create-secret --name openshift-mysecret03 --secret-string securedinformation3 --region us-east-2
```

- Create a secret in the final namespace to provide AWS credentials to external secrets:

```bash
echo -n 'xxxx' > ./secret-access-key
echo -n 'xxxx' > ./access-key
oc create secret generic awssm-secret --from-file=./access-key --from-file=./secret-access-key
```

- Add the configuration to the values file

```bash
secretStores:
  aws-secretstore:
    namespace: openshift-operators
    provider:
      aws:
        service: SecretsManager
        region: us-east-1
        auth:
          secretRef:
            accessKeyIDSecretRef:
              name: awssm-secret
              key: access-key
            secretAccessKeySecretRef:
              name: awssm-secret
              key: secret-access-key  
...
externalSecrets:
  aws-mysecret01:
    namespace: test
    refreshInterval: 5m
    secretStore: aws-secretstore
    matching:
      ocpSecretName: aws-openshift-mysecret01
      secrets:
        - originSecretName: openshift-mysecret01
          ocpSecretKey: privatedata
        - originSecretName: openshift-mysecret02
          ocpSecretKey: privatedata2
        - originSecretName: openshift-mysecret03
          ocpSecretKey: privatedata3
...
```

## Author

Asier Cidon @RedHat
