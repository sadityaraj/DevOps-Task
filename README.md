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
   
This configuration defines a frontend Deployment that manages 2 replicas of a pod running a container based on the aditya/image-frontend:v1 image.
A Service exposes the frontend on port 3000 within the cluster.
A HorizontalPodAutoscaler (HPA) is configured to automatically scale the number of pods between 2 and 5, based on CPU utilization.
        
   
2. Back-End:
   
Deployment: Manages 2 replicas of a backend API pod using the image aditya/mybackendimage. It also pulls MongoDB credentials (username and password) from a Kubernetes Secret (mongo-sec) and exposes port 8080.
Secret: Stores MongoDB credentials (username and password) in base64-encoded form.
Service: Exposes the API internally within the Kubernetes cluster using a ClusterIP on port 8080.
Horizontal Pod Autoscaler (HPA): Automatically scales the number of replicas between 2 and 5 based on CPU utilization (threshold: 50%).

Note: The values must be decoded (base64) before usage. Kubernetes handles this when the secret is injected into pods.

3. Database:
   
Deployment: Creates 1 MongoDB pod using the Docker image mongo:4.4.6. The MongoDB pod requests 512Mi of memory and 250m of CPU, with limits of 1Gi of memory and 500m of CPU. Environment variables for the root MongoDB user credentials are pulled from a Kubernetes secret (mongo-sec), which stores the username and password.

Service: Exposes the MongoDB pod internally within the cluster on port 27017, enabling other pods or services to connect to the MongoDB instance using the mongodb-svc Service. The Service routes traffic to pods with the label app: mongodb.

**Networking Configuration:**

1. ClusterIP Services:
    Internal networking is managed using ClusterIP services that expose the pods inside the Kubernetes cluster:
    The front-end communicates with the back-end via the backend-service.
    The back-end communicates with the database via the database-service.
2. Pod Autoscaling:
    Both front-end and back-end services utilize Horizontal Pod Autoscalers (HPA) to dynamically adjust the number of running pods based on CPU utilization 
    metrics. This ensures the application scales efficiently based on the demand.
