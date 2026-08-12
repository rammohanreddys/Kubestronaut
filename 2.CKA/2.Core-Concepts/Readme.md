## K8S Core Concepts:

### 0. Architecture

### 1. Compute & Workloads:

Kubernetes abstracts physical or virtual infrastructure into a unified compute platform through declarative API primitives. Workloads are modeled as high-level controller abstractions that manage the lifecycle of low-level execution units.

* Node (Node Components - Kubelet, KubeProxy, Container Runtime)
*  Pod  (Container, Init Container, Side Car Container, Shared Volumes, Network Namespace (Network + IPC))
*  Pod life Cycle
*  Deployment
*  StatefulSet

### 2. Networking Container:

Network Interface (CNI), Services: (ClusterIP, NodePort, Load-Balancer, Ingress), Cluster DNS, Istio,Gateway,VirtualService,Ingress,NetworkPolicy

### 3. Storage:

configMap, PersistentVolume (PV),PersistentVolumeClaim (PVC),StorageClass,Volumes. Dynamic Volumes Provisioning, CSI drivers

### 4. Data & Security:

RBAC(Role, Role-Binding, ClusterRole, ClusterRoleBinding),ServiceAccounts: PodSecurityStandards,Secrets.

### 5. Monitoring & Maintenance

Probes (Startup/Liveness/Readiness), DaemonSets: 

### 6. Pod Scheduling Concepts:

Taints & Tolerations,Node Selectors,Node Affinity,Pod affinity & Pod Anti-affinity

### 7. Scaling & High Availability:

ReplicaSet,HPA,VPA,ClusterAutoScaling,PodDisurptionBudget

### 8. Service Discovery/Traffic-Routing:

Service, Labels, Selectors, Ingress, Istio:


