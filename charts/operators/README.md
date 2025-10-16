# Openshift Cluster Bootstrap

This Helm chart installs operators in Openshift deploying the following elements:

- Operator Subscriptions
- Operator Groups
- Namespaces (*if they are required)

## Install

- Install Helm Chart

```$bash
helm install operators . 
```

- Install Helm Chart applying objects in kubernetes

```$bash
helm template . --debug | oc apply -f -
```

## Local Tests

- Lint

```$bash
helm lint
```

- Dry run installations

```$bash
helm install operators . --dry-run --debug
```

- Render templates Locally

```$bash
helm template . --debug
```

## Author Information

Asier Cidon @RedHat
