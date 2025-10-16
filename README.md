# Openshift GitOps

This repository contains a set of Helm Charts and Helm value files to manage different infrastructure components in multiple OCP clusters.

## Prerequisites

- Openshift +4.18
- Openshift GitOps +1.18.1

## Repo Structure

- *charts*: Helm Charts to implement OCP objects 
- *values*: Values files for every application, or Helm Chart, in specific OCP clusters (_*Each OpenShift Cluster should have a dedicated file for deploying the objects of its corresponding chart_)
- *applications*: ArgoCD Applications to configure every application in specific OCP clusters (_*Each OpenShift Cluster should have a dedicated application for deploying the objects of every chart_)
  - *app-of-apps*: ArgoCD App of Apps to configure automatically all the apps in specific OCP clusters

## App of Apps (Argo CD)

This repository is intended to be consumed by Argo CD using the "App of Apps" pattern. In this pattern a single Argo CD Application (the root application) points to a directory containing other Application manifests (children). The root Application keeps the collection of children in sync, enabling a hierarchical, single-entry deployment model.

Why use App of Apps
- Centralizes control: one root Application manages many related Applications (clusters/environments/services).
- Clear separation: this repo holds Chart and values; Argo CD Applications live under `applications/` and reference those charts/values.
- Easier lifecycle: you can promote or rollback groups of Applications together by updating the root Application.

Notes and best practices
- Use branch/tag targets (`targetRevision`) for reproducible deployments.
- Keep child Applications small and focused (one chart per Application) so rollbacks are predictable.
- Use `syncPolicy.automated.selfHeal` with care in production; it keeps resources reconciled automatically.
- Use `CreateNamespace=true` in the root Application if you want Argo CD to create namespaces declared by child Applications.
- Manage RBAC and Argo CD Projects to restrict which Applications can deploy where.

## Author 

Asier Cidon @RedHat
