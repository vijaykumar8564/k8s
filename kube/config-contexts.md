 To connect AKS cluster follow the steps
  - kubectl to be installed on client machine
  - az login
  - az account set --subscription 1d85cb44-c528-46ec-9320-8fb828a7e787
  - az aks get-credentials --resource-group cluster --name cluster-2 --overwrite-existing
  - kubectl get deployments --all-namespaces=true
  ## contexts
  -  kubectl config current-context   'Display the current-context'
  -  delete-cluster    Delete the specified cluster from the kubeconfig
  -  delete-context    Delete the specified context from the kubeconfig
  -  delete-user       Delete the specified user from the kubeconfig
  -  get-clusters      Display clusters defined in the kubeconfig
  -  get-contexts      Describe one or many contexts
  -  get-users         Display users defined in the kubeconfig
  -  rename-context    Rename a context from the kubeconfig file
  -  set               Set an individual value in a kubeconfig file
  -  set-cluster       Set a cluster entry in kubeconfig
  -  set-context       Set a context entry in kubeconfig
  -  set-credentials   Set a user entry in kubeconfig
  -  unset             Unset an individual value in a kubeconfig file
  -  use-context       Set the current-context in a kubeconfig file
  -  view              Display merged kubeconfig settings or a specified kubeconfig file  

  * kubectl config get-clusters
  ```python
  kubectl config get-clusters
    NAME
    cluster-1
    cluster-2
  ```

  * kubectl config get-users
  ```python
  kubectl config get-users

    NAME
    clusterUser_cluster_cluster-1
    clusterUser_cluster_cluster-2
  ```


  * kubectl config get-contexts
  ```python
  kubectl config get-contexts
    CURRENT   NAME        CLUSTER     AUTHINFO                        NAMESPACE
    *         cluster-1   cluster-1   clusterUser_cluster_cluster-1
              cluster-2   cluster-2   clusterUser_cluster_cluster-2
  ```
