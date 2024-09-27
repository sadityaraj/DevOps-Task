# DevOps-Task
Task provided by you

**Part 1: Terraform Script to Create AKS Cluster with Node Autoscaling**
Resource Group & AKS Cluster Creation--
    The Terraform script creates a resource group (azurerm_resource_group.aks_rg) to house the AKS cluster. The AKS cluster (azurerm_kubernetes_cluster.aks_cluster) 
    is configured with the following key features:

Node Autoscaling:
    The default node pool uses Azure's Standard_DS2_v2 VMs, with autoscaling enabled. The cluster scales from 1 to 5 nodes based on load, ensuring high availability 
    and efficient resource use.
Networking:
    The network profile uses the Azure CNI plugin for IP allocation and applies Calico as the network policy for security and isolation of pods.
Output:
    The script outputs the kube_config, which is used to connect to the cluster using kubectl.
