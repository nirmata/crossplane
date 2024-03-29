# crossplane

This repository contains the workflow to deploy the aks cluster

## Pre-Requisite:-

1. Azure CLI Access (Run below commands on terminal)
  ```
  az login
  ```
  It will redirect to azure login page, where user has to authenticate cli with azure credentials

  After authentication come back to cli, Run below command on terminal to see user details

  ```
  az account show
  ```


2. Connect to nirmata-devtest cluster


    Run the following commands

    Set the cluster subscription
    ```
    az account set --subscription baf89069-e8f3-46f8-b74e-c146931ce7a4
    ```
    Download cluster credentials
    ```
    az aks get-credentials --resource-group nirmata-devtest --name nirmata-devtest
    ```

    Verify the context, it should be "nirmata-devtest"
    ```
    kubectl config current-context
    ```

3. Use dev namespace to deploy the cluster claim file

  ```
   kubectl get namespace dev
  ```


## Steps to deploy the cluster claim file:-

  Clone the Repository locally

  ```
  git clone https://github.com/nirmata/crossplane.git
  cd crossplane
  ```

#### Naming Convention:- 



Name and id value should be same in cluster claim file (azure-aks-official.yaml)


Name can have (max 12 character)


special characters are not allowed (_, -, @, %, &, # etc.)

  ```
  name:- team_name+user_name+any_suffix
  ```
  ```
  Ex:- devarjunaks
       csdolisdemo
       qasathyatest
  ```

Edit the aks cluster claim (azure-aks-official.yaml) file and replace required values from the below section in the required parameters

  ```
  nodeSize: (small, medium, large)
  minNodeCount: (1-10)
  version: (1.25.6, 1.25.11, 1.26.3, 1.26.6, 1.27.1, 1.27.3, 1.28.0)
  ```

### Usage Example:-

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
      version: 1.27.1
  ```

Use dev namespace to deploy cluster claim

  ```
  kubectl apply -f azure-aks-official.yaml -n dev
  ```

See the cluster creation status, Cluster creation can take upto 10 minutes

  ```
  kubectl get managed
  ```
  Wait until you see READY and SYNCED status true

Ex:- 

  ```
  kubectl --namespace dev get clusterclaims
  ```

#### Replace clusterclaim_name with cluster claim name

  ```
  kubectl describe clusterclaim clusterclaim_name -n dev
  ```


Take the access of newly  created cluster  locally

#### Replace value of cluster id at "cluster_id"  place 

  Get the kubeconfig file
    ```
    kubectl get secret cluster_id-cluster -n dev
    ```

#### Replace value of cluster id at "cluster_id"  place 
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

    kubectl config current-context
    
  List the namespace in newly created cluster

    kubectl get namespaces

  There are some pre created namspace dev and production. It can be used for any operation.

Below step should only follow if you want to delete the cluster

To Delete the cluster

  ```
  kubectl delete -f azure-aks-official.yaml -n dev
  ```


## Troubleshooting

If you see cluster is not coming into ready state run below command to see the reason and make the changes accordingly.


See the resourcegroup status

Replace clusterid_name with your clusterid name
  ```
 kubectl get resourcegroup clusterid_name
  ```
  ```
 kubectl describe resourcegroup clusterid_name
  ```

See the kuberentes cluster status

Replace clusterid_name with your clusterid name

  ```
  kubectl get kubernetescluster clusterid_name
  ```
  ```
  kubectl describe kubernetescluster clusterid_name
  ```


  
Take the access of the newly created cluster locally
...

---

## Best Practices

1. **Keeping the Cluster on Azure:**
- To ensure that the cluster remains on Azure, manually update the "DoNoDelete" tag on the AKS cluster from the Azure console.

2. **Scaling Down Nodes:**
- You can scale down nodes by updating the node count to 0 in the cluster claim file. For example:

  ```yaml
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
      minNodeCount: 0
      version: 1.25.5
  ```

3. **Deleting the cluster:**
- After completing your work, you can delete the cluster using:

  ```shell
  kubectl delete -f azure-aks-official.yaml -n dev
  ```

4. **Follow Naming Conventions:**
- When creating cluster names and IDs, follow the naming conventions mentioned in the README for consistency and clarity.

