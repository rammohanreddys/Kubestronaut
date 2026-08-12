##################
Kubernetes abstracts physical or virtual infrastructure into a unified compute platform through declarative API primitives. Workloads are modeled as high-level controller abstractions that manage the lifecycle of low-level execution units.
1. Physical & Runtime Foundations: Nodes & Containers
+-------------------------------------------------------------------+
|                            NODE                                   |
|  +---------------------+  +---------------------+  +-----------+  |
|  |       kubelet       |  |   Container Runtime |  | kube-proxy|  |
|  +---------------------+  +---------------------+  +-----------+  |
|                                                                   |
|  +-------------------------------------------------------------+  |
|  |                            POD                              |  |
|  | [IPC Namespace] [Network Namespace (IP, Port, Routes)]      |  |
|  |                                                             |  |
|  |  +-----------------------+     +-------------------------+  |  |
|  |  | App Container         |     | Sidecar Container       |  |  |
|  |  | (Shared Volume Mount) |<--->| (Shared Volume Mount)   |  |  |
|  |  +-----------------------+     +-------------------------+  |  |
|  +-------------------------------------------------------------+  |
+-------------------------------------------------------------------+

Node
A Node is a worker machine (VM or physical server) in Kubernetes. It hosts the components required to run Pods:
 * kubelet: The primary node agent. It registers the node with the API server, watches for PodSpec assignments, and ensures assigned containers are running and healthy.
 * Container Runtime: Low-level software compliant with the Container Runtime Interface (CRI)—such as containerd or CRI-O—responsible for pulling images and executing containers via runc.
 * kube-proxy: Manages host network rules (using iptables or IPVS) to perform load balancing for Kubernetes Services.
Container
A Container is an isolated user-space environment created via Linux kernel primitives:
 * Namespaces: Provide resource isolation (pid, net, mnt, ipc, uts, user).
 * Control Groups (cgroups v1/v2): Enforce hard limits and soft shares for hardware resources (cpu, memory, io, pids).
2. The Atomic Execution Unit: Pods & Specialized Containers
Pod
A Pod is the smallest deployable unit in Kubernetes. It wraps one or more containers that share:
 * Network Namespace: All containers in a Pod share the same IP address, host name, and port space (localhost communication).
 * Storage (Volumes): Shared filesystems mounted at specified paths inside individual containers.
 * IPC Namespace: Shared System V IPC and POSIX message queues.
Init Containers
Init Containers execute sequentially and must run to completion before app containers start.
 * Execution Guarantee: Run strictly in the order defined in spec.initContainers. If any init container fails, the Pod restarts according to spec.restartPolicy.
 * Primary Use Cases: Pre-flight checks (database connectivity), running schema migrations, or downloading secrets/certificates required by main app containers.
Sidecar Containers
A Sidecar Container runs concurrently alongside the main application container within the same Pod to augment or extend its capabilities without modifying the core app image.
 * Native Sidecars (initContainers with restartPolicy: Always): Standardized mechanism where init containers are marked as persistent sidecars. They start sequentially before main app containers but remain running throughout the Pod's lifecycle.
 * Primary Use Cases: Log collection (Fluentbit), service mesh proxies (Envoy), dynamic secret renewal (Vault agent), and local metric scraping.
3. Pod Lifecycle & Health Monitoring
Pod Phases
A Pod's lifecycle is represented by its status.phase:
| Phase | Description |
|---|---|
| Pending | Pod created in API server; images are downloading or scheduler is binding to a Node. |
| Running | Bound to a Node; all init containers complete; at least one container is running/restarting. |
| Succeeded | All containers terminated successfully (exit code 0); will not be restarted. |
| Failed | All containers terminated, with at least one failing (non-zero exit code). |
| Unknown | State cannot be obtained, usually due to loss of node communication (kubelet down). |
Health Probes
Kubelet uses three types of probes to monitor container health via Exec, HTTPGet, or TCPSocket:
 * Startup Probe: Determines if the application inside the container has booted. All other probes are disabled until startup passes.
 * Liveness Probe: Checks if the container is deadlocked or broken. If it fails, kubelet kills the container and triggers its restartPolicy.
 * Readiness Probe: Checks if the container is ready to accept user traffic. If it fails, the Pod's IP address is removed from all matching Service endpoints.
4. Workload Controllers: Deployments vs. StatefulSets
Kubernetes uses high-level controllers to manage Pod creation, scaling, self-healing, and updates using control loops.
+-----------------------------------------------------------------+
|                       DEPLOYMENT / STATEFULSET                  |
+-----------------------------------------------------------------+
                               |
                               v
+-----------------------------------------------------------------+
|                          REPLICASET                             |
|  (Manages stateless identity & Pod scaling, e.g., pod-abcde)    |
+-----------------------------------------------------------------+
                               |
        +----------------------+----------------------+
        v                                             v
+---------------+                             +---------------+
|   Pod (web-1) |                             |   Pod (web-2) |
+---------------+                             +---------------+

Deployment
Deployments manage stateless workloads by leveraging an underlying ReplicaSet.
 * Pod Identity: Pods are completely interchangeable and ephemeral. Names are appended with random hash suffixes (e.g., web-78d579899c-x4z9l).
 * Storage: Shared across stateless pods, or dynamic ephemeral storage attached per pod.
 * Update Strategies:
   * RollingUpdate (default): Gradually replaces old Pods with new ones, controlled by maxUnavailable and maxSurge.
   * Recreate: Terminates all existing Pods before creating new ones (causes downtime).
StatefulSet
StatefulSets manage stateful workloads requiring unique network identities, persistent state, or ordered deployment.
 * Sticky Network Identity: Pods receive deterministic names based on ordinal indexes (app-0, app-1, app-2). They maintain stable DNS hostnames: $(pod-name).$(service-name).$(namespace).svc.cluster.local.
 * Persistent Storage via volumeClaimTemplates: Automatically provisions a dedicated PersistentVolumeClaim (PVC) for each Pod ordinal index. Volumes are not destroyed when Pods are scaled down, preserving state.
 * Ordered Operations:
   * Scale Up: Pods are created sequentially from 0 to N-1.
   * Scale Down: Pods are terminated in reverse order from N-1 to 0.
   * Rolling Updates: Updated sequentially in reverse ordinal order (Pod N-1 updated before Pod N-2).
Architectural Comparison: Deployment vs. StatefulSet
| Feature | Deployment | StatefulSet |
|---|---|---|
| Target Workloads | Microservices, Stateless Web APIs | Databases (PostgreSQL, MySQL), Distributed Stores (Kafka, ZooKeeper) |
| Pod Identity | Anonymous / Random (web-78d579-x4z9l) | Unique / Ordinal (db-0, db-1, db-2) |
| Persistent Storage | Ephemeral or shared multi-read volumes | Dedicated per-pod storage via volumeClaimTemplates |
| Network Identity | Dynamic IP via Service load balancer | Stable headless Service domain per pod instance |
| Ordering Guarantees | Parallel creation and destruction | Sequential creation/updates (0 \rightarrow N) & scale-down (N \rightarrow 0) |


------------------

Kubernetes networking implements a flat, IP-per-Pod network model where every Pod receives a unique, routable IP address within the cluster. It eliminates the need for port mapping between host and container, operating on a key design invariant: any Pod can communicate with any other Pod across nodes without Network Address Translation (NAT).
1. Physical Connectivity & CNI (Container Network Interface)
The Container Network Interface (CNI)
CNI is a Cloud Native Computing Foundation (CNCF) spec defining how network interfaces are configured when containers are created or destroyed. The kubelet delegates all networking setup to CNI plugins via JSON payloads.
+-------------------------------------------------------------------------------+
| NODE A                                                                        |
|                                                                               |
|  +-----------------------------------+     +-------------------------------+  |
|  | Pod A (10.244.1.2)               |     | Pod B (10.244.1.3)            |  |
|  |  +-----------------------------+  |     |  +-------------------------+  |  |
|  |  | Container (eth0)            |  |     |  | Container (eth0)        |  |  |
|  +--+--------------|--------------+--+     +--+------------|------------+--+  |
|                    | (veth pair)                               | (veth pair)  |
|                    v                                           v              |
|               vethA_podA                                  vethB_podB          |
|                    |                                           |              |
|  +-----------------|-------------------------------------------|-----------+  |
|  |                 +-------------> Bridge / eBPF <-------------+           |  |
|  |                                      |                                  |  |
|  |                           Node Routing Table / Overlay                  |  |
|  +--------------------------------------|----------------------------------+  |
|                                         v                                     |
|                                Node Interface (eth0: 192.168.1.10)           |
+-------------------------------------------------------------------------------+

Packet Flow & CNI Architectures
When a Pod is scheduled to a Node, the CNI plugin performs three key setup tasks:
 * Veth Pair Creation: Creates a virtual ethernet pair (veth). One interface is placed inside the Pod’s network namespace (renamed to eth0), and the other end remains in the host network namespace (e.g., veth1234).
 * IP Allocation (IPAM): Assigns an IP from the Node's allocated Pod CIDR subnet to the Pod interface.
 * Routing & Data Path Enforcement: Connects the host end of the veth pair to the node's network pipeline using one of two primary architectural models:
 * Overlay Networks (e.g., Flannel VXLAN, Calico VXLAN): Encapsulates Layer 2/3 Pod packets inside Layer 4 UDP packets (VXLAN port 4789). This abstracts underlying physical network constraints, allowing Pod traffic to span across heterogeneous subnets at the cost of slight encapsulation overhead.
 * Non-Overlay Direct Routing (e.g., Calico BGP, Cilium eBPF, AWS-VPC CNI): Avoids packet encapsulation entirely.
   * Calico (BGP): Uses BGP protocol to peer host node routing tables directly with top-of-rack (ToR) physical switches.
   * Cilium (eBPF): Bypasses the Linux kernel network stack (iptables/netfilter) entirely. It attaches eBPF programs directly to Linux network sockets and TC (Traffic Control) hooks to route packets at near-native hardware speed.
2. Core Service Abstractions & Traffic Routing
Pods are dynamic and ephemeral. Services provide stable IP addresses, DNS entries, and Layer 4 load balancing across a dynamic set of Pods matched by spec.selector.
                        +----------------------------+
                        |  External Client / Traffic |
                        +----------------------------+
                                      |
                                      v
                        +----------------------------+
                        |   LoadBalancer / Ingress   |
                        +----------------------------+
                                      |
                                      v
                        +----------------------------+
                        |          NodePort          | (HostIP:30000-32767)
                        +----------------------------+
                                      |
                                      v
                        +----------------------------+
                        |         ClusterIP          | (Internal Cluster IP)
                        +----------------------------+
                                      |
                 +--------------------+--------------------+
                 v                                         v
        +-----------------+                       +-----------------+
        | Pod A (10.244.1.2)                      | Pod B (10.244.2.4)
        +-----------------+                       +-----------------+

1. ClusterIP (Default)
Exposes the Service on an internal, unroutable cluster-virtual IP address.
 * Scope: Accessible only within the cluster.
 * Mechanism: kube-proxy writes routing rules on each node (iptables PREROUTING/KUBE-SERVICES chains or IPVS virtual servers). When traffic hits the Virtual IP, rules perform DNAT (Destination NAT) to replace the ClusterIP with a healthy backend Pod's real IP address.
2. NodePort
Extends ClusterIP by allocating a high-range port (30000-32767) across every Node in the cluster.
 * Scope: External clients hit <NodeIP>:<NodePort> to reach the Service.
 * Mechanism: Traffic arriving on any Node's physical IP at the assigned NodePort is routed via iptables/IPVS to the internal ClusterIP, which then forwards it to a destination Pod.
3. LoadBalancer
Extends NodePort by integrating with a cloud provider API (AWS ELB/NLB, GCP Cloud Load Balancing, Azure LB) or bare-metal load balancer (MetalLB).
 * Scope: Exposes an external, publicly accessible IP address.
 * Mechanism: Automatically provisions an external physical/cloud infrastructure load balancer that targets the cluster's <NodeIP>:<NodePort> nodes.
4. Ingress
An Ingress is an HTTP/HTTPS Layer 7 application-level routing abstraction (not a Service type).
 * Mechanism: Requires an Ingress Controller (e.g., ingress-nginx, Envoy, Traefik) running inside the cluster. It reads Ingress resources and configures a reverse proxy to route incoming HTTP requests based on Host header (api.domain.com) or URI Path (/v1/users) directly to backing ClusterIP services.
3. Cluster DNS (CoreDNS)
CoreDNS is the cluster-internal service discovery engine running as a deployment in the kube-system namespace. It listens on a dedicated ClusterIP (commonly 10.96.0.10).
DNS Resolution Architecture
When a container starts, kubelet populates its /etc/resolv.conf file:
nameserver 10.96.0.10
search default.svc.cluster.local svc.cluster.local cluster.local
options ndots:5

Standardized Record Naming Protocols
 * Standard Service: <service-name>.<namespace>.svc.cluster.local \rightarrow Resolves to the ClusterIP.
 * Headless Service (clusterIP: None): <service-name>.<namespace>.svc.cluster.local \rightarrow Resolves directly to an A/AAAA record array containing all individual backend Pod IPs.
 * Individual Pod Record: <pod-ip-with-hyphens>.<namespace>.pod.cluster.local (e.g., 10-244-1-2.default.pod.cluster.local).
4. Network Security: NetworkPolicies
By default, Kubernetes networking operates in a non-isolated mode (all-to-all communication). NetworkPolicies provide zero-trust Layer 3/4 firewall rules using label selectors.
                        +-----------------------+
                        |  Frontend Pod         |
                        |  role: frontend       |
                        +-----------------------+
                                    |
                                    |  TCP / Port 5432 (ALLOWED)
                                    v
+-----------------------------------------------------------------------+
|  DATABASE POD (role: db)                                              |
|                                                                       |
|  Ingress Policy:                                                      |
|  - Allow FROM podSelector: { role: frontend } ON port: 5432         |
|  - Deny ALL OTHER traffic (Default Isolation)                         |
+-----------------------------------------------------------------------+
                                    ^
                                    |  TCP / Port 5432 (BLOCKED)
                        +-----------------------+
                        |  Analytics Pod        |
                        |  role: analytics      |
                        +-----------------------+

Enforcement Engine
NetworkPolicies are declarative API definitions—Kubernetes does not enforce them natively. An active CNI plugin supporting NetworkPolicy primitives (e.g., Calico, Cilium, Weave) must be installed. The CNI translates policy specs into kernel-level dynamic rules (iptables, eBPF, or IPSet).
Structural Specification
 * podSelector: Defines the set of target Pods to which the policy applies.
 * policyTypes: Ingress (incoming), Egress (outgoing), or both.
 * Isolation Logic: Once a Pod is selected by any policy, it becomes isolated and drops all non-explicitly allowed traffic.
5. Service Mesh: Istio & K8s Gateway API
Standard Ingress and Services operate at basic Layer 4 and Layer 7 entry points. A Service Mesh like Istio provides fine-grained Layer 7 control, operational observability, and cryptographic security across service-to-service communications.
+---------------------------------------------------------------------------+
| ISTIO SERVICE MESH CONTROL PLANE (Istiod)                                 |
| (Translates VirtualServices & Gateways into Envoy Configurations)         |
+---------------------------------------------------------------------------+
                                    |
                                    v (xDS Protocol)
+---------------------------------------------------------------------------+
| DATA PLANE                                                                |
|                                                                           |
|   +--------------------------+           +----------------------------+   |
|   | Pod A                    |  mTLS     | Pod B                      |   |
|   |  +--------------------+  | (mTLS/    |  +----------------------+  |   |
|   |  | App Container      |  |  TCP)     |  | App Container        |  |   |
|   |  +---------|----------+  |           |  +----------^-----------+  |   |
|   |            v             |           |             |              |   |
|   |  +--------------------+  |           |  +----------|-----------+  |   |
|   |  | Envoy Sidecar Proxy|==|===========|=>| Envoy Sidecar Proxy  |  |   |
|   |  +--------------------+  |           |  +----------------------+  |   |
|   +--------------------------+           +----------------------------+   |
+---------------------------------------------------------------------------+

Data Plane & Control Plane Architecture
 * Control Plane (istiod): Compiles high-level routing rules and security policies into low-level configuration payloads, distributing them using the xDS API.
 * Data Plane (Envoy Proxies): High-performance reverse proxies deployed alongside app containers (via dynamic sidecar injection or ambient node-level ztunnels). All incoming/outgoing Pod network traffic is intercepted via iptables redirect rules and passed through Envoy.
Key Istio & Gateway Primitives
1. Gateways (Istio Gateway & Kubernetes Gateway API)
 * Deploys an Envoy-based proxy edge load balancer operating at the cluster perimeter. It manages incoming/outgoing HTTP/TCP endpoints, TLS termination, and host port exposure.
2. VirtualService
 * Defines dynamic Layer 7 traffic routing rules applied to traffic hitting a Gateway or an internal mesh service.
 * Key Capabilities:
   * Traffic Splitting: Route 90% of traffic to v1 and 10% to v2 (Canary Releases).
   * Fault Injection: Intentionally introduce latency or HTTP 500 error status responses to test resilient app design.
   * Retries & Timeouts: Automatically trigger request retries with exponential backoffs.
3. DestinationRule
 * Configures policies applied to traffic after routing decision resolution via VirtualService.
 * Key Capabilities:
   * Load Balancing Algorithms: Set backend pool balancing to ROUND_ROBIN, LEAST_REQUEST, or RANDOM.
   * Circuit Breaking: Set max connections, dynamic request queues, and outlier detection (e.g., automatically eject instance from pool if it returns three consecutive 50x errors).
4. Mutual TLS (mTLS)
 * istiod acts as an automated Certificate Authority (CA). Envoy proxies automatically handle SPIFFE identity verification and dynamic TLS key exchanges, providing end-to-end encrypted wire transit and cryptographically verified service identities across all mesh workloads.
Comparison: Traffic Routing Layers
| Primitive | Layer | Target Domain | Key Use Case |
|---|---|---|---|
| ClusterIP / Service | Layer 4 | Internal Pod Groups | Basic TCP/UDP internal load balancing |
| Ingress | Layer 7 | Cluster Boundary | Basic HTTP host/path routing and SSL termination |
| K8s Gateway API | Layer 4-7 | Cluster Boundary | Role-oriented (Infra vs App Admin) perimeter traffic control |
| Istio VirtualService | Layer 7 | Mesh Internal / Edge | Canary deployments, circuit breaking, and traffic mirroring |
| NetworkPolicy | Layer 3-4 | Host/Pod Interfaces | Zero-trust IP/Port firewall filtering |
