# crossplane

This repository contains the workflow to deploy the aks cluster

Pre-Req:-

1. Azure CLI Access
2. Necessary Access to deploy the aks cluster
3. Access to infra namespace in crossplane aks cluster

Clone the Repository locally

Edit the aks cluster claim file and replace with the below values in the required parameters

```
nodeSize: (small, medium, large)
minNodeCount: (1-10)
version: (1.24.9, 1.24.10, 1.25.5, 1.25.6, 1.26.0, 1.26.3, 1.27.1)
```

## Usage Example:-

Cluster claim example file:-

```
apiVersion: devopstoolkitseries.com/v1alpha1
kind: ClusterClaim
metadata:
  name: az-team-aks
spec:
  id: azteamaks
  compositionSelector:
    matchLabels:
      provider: azure-official
      cluster: aks
  parameters:
    nodeSize: medium
    minNodeCount: 1
    version: 1.25.5
```

Use infra namespace to deploy cluster claim

```
git clone https://github.com/nirmata/crossplane.git
cd crossplane
```

Edit yaml file and make necessary changes

```
kubectl apply -f azure-aks-official.yaml -n infra
```


To Delete the cluster

```
kubectl delete -f azure-aks-official.yaml -n infra
```
