## K8S Architecture

Kubernetes (K8S) follows a master-worker distributed architecture. A Cluster is split into two primary layers.

The **Control-Plane** (the "Brain" that makes decisions) and **Worker-Nodes** (the "muscle" that runs your application workloads.)

<p align="center">
  <img src="../Images/Architecture.png" alt="K8S-Architecture" width="600">
</p>

#### 1. Control-Plane (Master-Node):

The Control Plane manages the global state of the cluster, processes incoming API requests, schedules workloads and responds to cluster events.

* **Kube-Apiserver** (Front End): The central entry point for the entire cluster. All administrative commands (kubectl, UIs or internal components) communicate through this RESTful API.
  
* **Etcd** (Database): A high available, distributed key-value store that serves as the single source of truth. It holds all cluster configuration data and current operational states.
  
* **Kube-Scheduler** (Decision Maker): Responsible for assigning newly created pods to appropriate worker nodes based on CPU/Memory resource requirements, affinity rules and node constraints.
  
* **Kube-Controller-Manager** (State Enforcer): Runs continuous background controller loops (E.g., Node Controller, Deployment Controller). It compares the actual state of the cluster agaist the desired state and make adjustments if they don't match.
  
* **Cloud-Controller-Manager** (Cloud Integrator): Connects your cluster to a cloud provider's API (AWS, GCP, AZURE) to manage resources like load balancers and storage volumes.
