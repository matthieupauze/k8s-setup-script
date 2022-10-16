# Dashboard Setup

Dashboard is a commonly used tool for managing kubernetes clusters.
It should be used alongside ArgoCD for managing namespaces, roles, persistent volumes, etc.

## Installation

Start by running the following command.

This will create the `kubernetes-dashboard` namespace and create the baseline pods and services for dashboard.

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.6.1/aio/deploy/recommended.yaml
```

## Usage / Configuration

TODO
