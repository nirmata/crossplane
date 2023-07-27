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
   kubectl get namespace dev
  ```
=====================================================

Steps to deploy the cluster claim file:-

  Clone the Repository locally

  ```
  git clone https://github.com/nirmata/crossplane.git
  cd crossplane
  ```

Naming Convention:- 



Name and id value should be same in cluster claim file (azure-aks-official.yaml)
Name can have (max 12 character)
special character are not allowed (_, -, @, %, &, # etc.)

  ```
  name:- team_name+user_name+any_suffix
  ```
  ```
  Ex:- devarjunaks
       csdolisdemo
       qasathyatest
  ```

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
    name: csvikasaks
    namespace: dev
  spec:
    id: csvikasaks 
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

See the cluster creation status, Cluster creation can take upto 15 minutes

  ```
  kubectl get managed
  ```
  Wait until you see READY and SYNCED status true

Ex:- 

  ```
  kubectl --namespace dev get clusterclaims
  ```

### Replace clusterclaim_name with cluster claim name

  ```
  kubectl describe clusterclaim clusterclaim_name -n dev
  ```


Take the newly create cluster access locally

### Replace value of cluster id at "cluster_id"  place 

  Get the kubeconfig file
    ```
    kubectl get secret cluster_id-cluster -n dev
    ```

### Replace value of cluster id at "cluster_id"  place 
  ```
  kubectl --namespace dev \
      get secret cluster_id-cluster \
      --output jsonpath="{.data.kubeconfig}" \
      | base64 -d >kubeconfig.yaml
  ```
  ```
  Usage Example:- 
  # kubectl --namespace dev \
      get secret csvikash-cluster \
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


  Verify the context, it should be "cluster_id"

    ```
    kubectl config current-context
    ```

Below step should only follow if you want to delete the cluster

To Delete the cluster

  ```
  kubectl delete -f azure-aks-official.yaml -n dev
  ```
