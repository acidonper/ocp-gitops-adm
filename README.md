# Openshift GitOps

This repository contains a set of Helm Charts and Helm value files to manage different infrastructure components in multiple OCP clusters.

## Prerequisites

- Openshift +4.18
- Openshift GitOps +1.18.1

## Repo Structure

- *charts*: Helm Charts to implement OCP objects 
- *values*: Values files for every application, or Helm Chart, in specific OCP clusters (_*Each OpenShift Cluster should have a dedicated file for deploying the objects of its corresponding chart_)
- *applications*: ArgoCD Application to configure every application in specific OCP clusters (_*Each OpenShift Cluster should have a dedicated application for deploying the objects of every chart_)

## Author 

Asier Cidon @RedHat
