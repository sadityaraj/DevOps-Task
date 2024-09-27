# DevOps-Task
Task provided by you

**Terraform Script to Create AKS Cluster with Node Autoscaling**

1. Resource Group & AKS Cluster Creation--
    The Terraform script creates a resource group (azurerm_resource_group.aks_rg) to house the AKS cluster. The AKS cluster
    (azurerm_kubernetes_cluster.aks_cluster) is configured with the following key features:
   Node Autoscaling:
            The default node pool uses Azure's Standard_DS2_v2 VMs, with autoscaling enabled. The cluster scales from 1 to 2 nodes based on load, ensuring high 
            availability and efficient resource use.
   Networking:
            The network profile uses the Azure CNI plugin for IP allocation and applies Calico as the network policy for security and isolation of pods.
   Output:
            The script outputs the kube_config, which is used to connect to the cluster using kubectl.

**Kubernetes Manifests Explanation**

The Kubernetes manifest files are used to define deployments for the front-end, back-end, and database, as well as the services and autoscalers for each component.

Front-End, Back-End, and Database Deployments:

1. Front-End:
    Deployed with 2 replicas, exposed via a ClusterIP service on port 80.
    An HorizontalPodAutoscaler (HPA) monitors the CPU utilization and scales the replicas between 2 and 5.

2. Back-End:
    Similar to the front-end, deployed with 2 replicas, exposed on port 8080, and with an HPA for CPU-based scaling between 2 and 5 replicas.

3. Database:
    Deployed with 1 replica (no autoscaling needed). Exposed internally via a ClusterIP service on port 5432 for the back-end to connect.
    
**Networking Configuration:**

1. ClusterIP Services:
    Internal networking is managed using ClusterIP services that expose the pods inside the Kubernetes cluster:
    The front-end communicates with the back-end via the backend-service.
    The back-end communicates with the database via the database-service.
2. Pod Autoscaling:
    Both front-end and back-end services utilize Horizontal Pod Autoscalers (HPA) to dynamically adjust the number of running pods based on CPU utilization metrics. This ensures the application scales efficiently based on the demand.
