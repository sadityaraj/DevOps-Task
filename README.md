![K8s Diagram](https://github.com/user-attachments/assets/bfab0c86-2405-421c-af5e-1e9f7af5e900)# DevOps-Task
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

**K8s Diagram Explanation:**
  I have implemented both architecture and networking diagram in one. But here is the below explanation on the logic behind the network floe and the architecture 
  diagram. 
  
The architecture diagram illustrates the overall structure of a multi-tier application deployed on a Kubernetes cluster. Here's a detailed breakdown of its components and their interactions:

**Components:**

Frontend Pod:
   This represents the user interface of the application. It could be a web application built with frameworks like React, Angular.
   The frontend pod is responsible for handling user interactions and displaying data fetched from the backend.
   
Backend Pod:
  This serves as the application's business logic layer. It processes requests from the frontend and interacts with the database to retrieve or manipulate data.
  The backend could be built using technologies like Node.js, Java, or Python, and typically exposes RESTful APIs or GraphQL endpoints for the frontend to consume.

Database Pod:
  This represents the persistent data storage layer of the application. It could be any relational or NoSQL database (e.g., PostgreSQL, MongoDB).
  The database pod is responsible for storing application data and is accessed by the backend for data retrieval and updates using secrets.
  
**Communication Flows:**

API Requests:
    Arrows from the frontend to the backend indicate that the frontend sends API requests to the backend pod to fetch data or perform actions based on user input.
    This is typically done using HTTP/HTTPS protocols.
    
Database Queries:
    Arrows from the backend to the database represent the backend's interaction with the database. This includes executing queries to retrieve data or update 
    records.
    The backend translates the API requests from the frontend into database queries.
    
Kubernetes Context:
    Each component (frontend, backend, database) is encapsulated in a Kubernetes pod, allowing for easy deployment, scaling, and management of the application 
    components.
    Kubernetes handles the orchestration, ensuring that the necessary pods are running and managing their lifecycle.
    
Scalability:
    The architecture allows for horizontal scaling, where multiple instances of the frontend and backend can be deployed to handle increased traffic usinr HPA.
    The database can also be configured for replication or sharding to improve performance and availability.
    
**Network Flow Explanation**
The network diagram complements the architecture by detailing how the different components communicate over the network. Here's a breakdown of the network 
aspects:

 Network Policies:

  The diagram may include indications of network policies (like using Calico) that enforce rules governing the traffic flow between the pods.
  For example, it can restrict access to the database pod, allowing only the backend pod to communicate with it, enhancing security.
    Traffic Flow:
     Frontend to Backend:
        The network diagram illustrates the flow of HTTP requests from the frontend to the backend.
        This communication is often secured using HTTPS to protect data in transit.
     Backend to Database:
        The flow from the backend to the database indicates how the backend makes SQL or NoSQL queries to interact with the data layer.
        The backend pod usually communicates with the database using the internal IP address assigned by Kubernetes, ensuring that external traffic does not reach 
        the database directly.
     Load Balancing:
        Depending on the setup, there may be indications of load balancers managing traffic to the frontend and backend pods.
        Load balancers distribute incoming requests among multiple pod instances to improve availability and performance.

