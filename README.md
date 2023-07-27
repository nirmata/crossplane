# crossplane

This repository contains the workflow to deploy the aks cluster

Pre-Req:-

1. Azure CLI Access
  ```
  az login
  ```
  It will redirect you to azure login page, where user has to authenticate cli with azure credentials
  After authentication come back to cli, Run below command to see user details

  ```
  az account show
  ```


2. Connect to crossplane cluster


    Run the following commands
    Set the cluster subscription
    ```
    az account set --subscription baf89069-e8f3-46f8-b74e-c146931ce7a4
    ```
    Download cluster credentials
    ```
    az aks get-credentials --resource-group crossplane --name crossplane
    ```

    Verify the context, it should be "crossplane"
    ```
    kubectl config current-context
    ```

3. Use dev namespace to deploy the cluster claim file

  ```
   kubectl get namespace -n dev
  ```
=====================================================

Steps to deploy the cluster claim file:-

  Clone the Repository locally

  ```
  git clone https://github.com/nirmata/crossplane.git
  cd crossplane
  ```

Naming Convention to be followed in cluster claim file (azure-aks-official.yaml):-

Name and id value should be same in cluster claim file (azure-aks-official.yaml)

  name:- team_name-user_name-any_suffix
  Ex:- dev-arjun-aks
      cs-dolis-demo
      qa-sathya-test


Edit the aks cluster claim (azure-aks-official.yaml) file and replace with the below values in the required parameters

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
    name: cs-vikash-test
    namespace: dev
  spec:
    id: cs-vikash-test
    compositionSelector:
      matchLabels:
        provider: azure-official
        cluster: aks
    parameters:
      nodeSize: medium
      minNodeCount: 1
      version: 1.25.5
  ```

Use dev namespace to deploy cluster claim

  ```
  kubectl apply -f azure-aks-official.yaml -n dev
  ```

See the cluster creation status

  kubectl get managed
  kubectl --namespace dev get clusterclaims
  kubectl describe clusterclaim clusterclaim_name -n dev


Take the cluster access locally

# Replace value of cluster id at id place and namespace at ns_value

  ```
  kubectl --namespace ns_value \
      get secret id-cluster \
      --output jsonpath="{.data.kubeconfig}" \
      | base64 -d >kubeconfig.yaml
  ```
  ```
  cat kubeconfig.yaml
  ```
  ```
  export KUBECONFIG=$PWD/kubeconfig.yaml
  ```
  ```
  kubectl get namespaces
  ```

To Delete the cluster

  ```
  kubectl delete -f azure-aks-official.yaml -n dev
  ```
