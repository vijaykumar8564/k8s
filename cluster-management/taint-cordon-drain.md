# Cluster Management Commands:
  * cluster-info    Display cluster information
  * top             Display resource (CPU/memory) usage
  * cordon          Mark node as unschedulable
  ```python
  kubectl cordon <node-name>
  The kubectl cordon command is used in Kubernetes to mark a node as unschedulable. When you "cordon" a node, Kubernetes will prevent new pods from being scheduled onto that node. However, it allows existing pods on the node to continue running until they terminate or are manually moved elsewhere.

  ```
  * uncordon        Mark node as schedulable
  * drain           Drain node in preparation for maintenance
  * taint           Update the taints on one or more nodes
