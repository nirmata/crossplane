# crossplane
This repository contains the workflow to deploy the aks cluster


Clone the Repo locally

Edit the aks cluster claim file and add the below values in the parameters

```
nodeSize: (small, medium, large)
minNodeCount: (1-10)
version: (1.24.9, 1.24.10, 1.25.5, 1.25.6, 1.26.0, 1.26.3, 1.27.1)
```

## Usage Example:-

Cluster claim file example:-

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

User infra namespace

```
git clone https://github.com/nirmata/crossplane.git
cd crossplane
```

Edit yaml file and make necessary changes

```
k apply -f azure-aks-official.yaml -n infra
```