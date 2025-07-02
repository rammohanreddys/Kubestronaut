# K8S - Scheduling

Download Presentation Deck for this section from documents folder:
- Kubernetes+-CKA-+0200+-+Scheduling.pdf
- Networking.pdf
- Static+Pods.pdf
- Udemy+Kubernetes+taints-tolerations.pdf

## Manual Scheduling Pods or No Scheduler:

In Kubernetes, manual scheduling means explicitly assigning a pod to a specific node, bypassing the default scheduler. This can be done using the **nodeName** field 
in the pod spec or using nodeSelector, node affinity, or taints and tolerations.

**Hard Scheduling or Manual Scheduling:**

This directly places the pod on the specified node. Kubernetes skips the scheduler for such pods.
```
apiVersion: v1
kind: Pod
metadata:
  name: manual-pod
spec:
  nodeName: <node-name>   # e.g., minikube, ip-10-0-0-1
  containers:
  - name: nginx
    image: nginx
```
**Note:**

You cannot directly "schedule" existing pods onto specific nodes manually because once a Pod is created in Kubernetes, 
its scheduling is already completed by the control plane and it is bound to a node.

However, there are ways to influence pod placement or migrate existing pods manually by following certain methods:
  1. Delete and Recreate Pods with Node Affinity / NodeSelector
  2. Using kubectl cordon + kubectl drain
  3. Use kubectl patch or edit Deployment
  4. Manually delete and recreate the pod with --nodeName

 ## Labels, Selectors & Annotations:

 Labels and Selectors are core concepts in Kubernetes used to organize, filter, and select resources (like pods, services, deployments, etc.).

 ### What is a Label?

 A label is a key-value pair attached to Kubernetes objects (e.g., pods, nodes, services) to give them identifying metadata.
 Example:
 ```
 metadata:
   labels:
     app: nginx
     tier: frontend
     env: prod
  ```
### What is a Selector?

A selector is a query used to match resources based on their labels.

**Example:** A Service selects pods with app: nginx.

**Example: Label in Pod YAML**

```
apiVersion: v1
kind: Pod
metadata:
  name: mypod
  labels:
    app: nginx
    env: dev
spec:
  containers:
  - name: nginx
    image: nginx

```

**Example: Selector in a Service**

```
apiVersion: v1
kind: Service
metadata:
  name: myservice
spec:
  selector:
    app: nginx
    env: dev
  ports:
    - port: 80
      targetPort: 8080
```
`This service will route traffic to pods that have both app=nginx and env=dev.`

### Types of Label Selectors:
1. Equality-Based (default)
   - key = value
   - key != value

```
selector:
  matchLabels:
    app: nginx
```

2. Set-Based (used with matchExpressions)
   `in, notin, exists, !`

```
selector:
  matchExpressions:
  - key: app
    operator: In
    values:
    - nginx
    - apache
```

#### Summary:

| Concept      | Purpose                                        |
| ------------ | ---------------------------------------------- |
| **Label**    | Tagging resources                              |
| **Selector** | Finding or grouping resources based on labels  |
| **Used by**  | Services, Deployments, ReplicaSets, Scheduling |


#### kubectl Commands with Labels:

| Action             | Command                            |
| ------------------ | ---------------------------------- |
| Add label to a pod | `kubectl label <resource-type> <resource-name> <key>=<value>` |
|                    | Ex: `kubectl label pod mypod env=prod`|
|Add Multiple Labels at Once| `kubectl label pod mypod env=dev version=1.0`|
|Update (Overwrite) an Existing Label| `kubectl label pod mypod env=prod --overwrite`|
|Remove a Label from a Resource| `kubectl label pod mypod env- `|
|List Resources with Labels| `kubectl get pods --show-labels`|
|Filter Resources by Label| `kubectl get pods -l app=nginx`|
| | `kubectl get svc -l env=prod,tier=frontend`|
| | `kubectl get pods -l 'env!=prod'`|
| | `kubectl get pods -l 'tier in (frontend,backend)'`|
|Label All Pods in a Namespace| `kubectl label pods --all team=devops`|

#### kubectl Commands with Selector:

```
kubectl get <resource> --selector=<label-key>=<label-value>
```

### Anotations:

#### What Are Annotations in Kubernetes?

Annotations are key-value metadata attached to Kubernetes objects, similar to labels — but not used for selection or filtering.

They are meant to store non-identifying metadata, like:

- Tooling instructions
- Debug information
- Deployment history
- Links to CI/CD systems
- Checksums, timestamps, or URLs

#### Key Differences: Labels vs Annotations

| Feature                       | Labels                        | Annotations                              |
| ----------------------------- | ----------------------------- | ---------------------------------------- |
| Used for selecting/filtering? | ✅ Yes                         | ❌ No                                     |
| Size limit                    | Small (optimized for queries) | Large metadata blobs allowed             |
| Use case                      | Grouping and filtering        | Storing additional, descriptive metadata |

#### Common Use Cases:

| Use Case                    | Annotation Example                                 |
| --------------------------- | -------------------------------------------------- |
| Monitoring                  | `prometheus.io/scrape: "true"`                     |
| Last applied config         | `kubectl.kubernetes.io/last-applied-configuration` |
| Auto-scaling hints          | `autoscaling.alpha.kubernetes.io/metrics`          |
| Istio / Service Mesh config | `sidecar.istio.io/inject: "false"`                 |
| GitOps tools                | `argocd.argoproj.io/instance: "my-app"`            |

#### Commands for Annotations:

1. Add/Update an annotation:
```
kubectl annotate <resource> <name> <key>=<value> [--overwrite]
kubectl annotate pod mypod description="debug enabled" --overwrite
```

2. View annotations:
```
kubectl get pod mypod -o jsonpath='{.metadata.annotations}'
```

3. Remove an annotation:
```
kubectl annotate pod mypod description-
```

## Taints & Tolerations:

Taints and Tolerations work together to control which pods can be scheduled on which nodes.

They are commonly used to:
 - Keep critical workloads isolated
 - Prevent certain pods from being scheduled on specific nodes
 - Implement dedicated nodes, such as GPU or infra nodes

### What is a Taint?

A taint is applied to a node and prevents pods from being scheduled on it unless those pods tolerate the taint.

Syntax:
```
kubectl taint nodes <node-name> <key>=<value>:<effect>
```
**Effects:**

| Effect             | Description                                                    |
| ------------------ | -------------------------------------------------------------- |
| `NoSchedule`       | Pods that do not tolerate the taint will not be scheduled      |
| `PreferNoSchedule` | Scheduler tries to avoid placing pods, but it's not guaranteed |
| `NoExecute`        | Evicts running pods that don't tolerate it                     |

**Example: Apply a Taint to a Node**
```
kubectl taint nodes node1 dedicated=infra:NoSchedule
```
This means: `Only pods with a matching toleration can be scheduled on node1.`

**Check Existing Taints on Nodes**
```
kubectl describe node <node-name> | grep Taint
```
**Remove a Taint**
```
kubectl taint nodes <node-name> <key>-  # Remove by key
kubectl taint nodes node1 dedicated-
```

### What is a Toleration?

A toleration is applied to a pod (template) and allows it to bypass (tolerate) a node’s taint.

**Example:**

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "infra"
    effect: "NoSchedule"
  containers:
  - name: nginx
    image: nginx
```

#### What is operator in Tolerations in Kubernetes?

The operator in a toleration defines how the pod’s toleration is matched against a node’s taint

**Supported operator Values**:

| Operator | Description                                                           |
| -------- | --------------------------------------------------------------------- |
| `Equal`  | (Default) Pod tolerates a taint if the `key` **and** `value` match    |
| `Exists` | Pod tolerates a taint if the `key` matches — the value is **ignored** |

#### 1. Equal Operator:

This is the default. The pod must have a toleration that exactly matches the taint's key, value, and effect.

##### Example: 

**Node Taint**

```
kubectl taint nodes node1 dedicated=infra:NoSchedule
```

**Pod toleration:**
```
tolerations:
- key: "dedicated"
  operator: "Equal"
  value: "infra"
  effect: "NoSchedule"
```
This pod will match and be scheduled on node1.

#### 2. Exists Operator

Only the key and effect are required. The value is ignored.

##### Example:

**Node taint:**
```
kubectl taint nodes node2 team=devops:NoSchedule
```
**Pod toleration:**
```
tolerations:
- key: "team"
  operator: "Exists"
  effect: "NoSchedule"
```
This toleration matches any value for team, allowing the pod to run on the tainted node.

**Summary Table:**

| Taint on Node         | Toleration on Pod                     | Matches? |
| --------------------- | ------------------------------------- | -------- |
| `env=prod:NoSchedule` | `key=env, value=prod, operator=Equal` | ✅ Yes    |
| `env=prod:NoSchedule` | `key=env, operator=Exists`            | ✅ Yes    |
| `env=prod:NoSchedule` | `key=env, value=dev, operator=Equal`  | ❌ No     |
| `env=prod:NoSchedule` | `key=zone, operator=Exists`           | ❌ No     |

## Node Selectors:

### What is a Node Selector in Kubernetes?

A Node Selector is the simplest way to schedule pods to specific nodes based on node labels. It lets you control where your pod runs by specifying that it should only be placed on nodes with matching labels.

**Use Case:**

Use nodeSelector when you want your pod to run only on nodes with a specific label, 
e.g.:
 - Workloads that require GPU nodes
 - Segregating prod/dev workloads
 - Running a pod only on a specific region/zone

**Add a Label to a Node:**
```
kubectl label nodes node1 workload=frontend
```
**Use nodeSelector in a Pod/Deployment:**

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    workload: frontend
  containers:
  - name: nginx
    image: nginx
```

`This pod will only be scheduled on nodes labeled workload=frontend.`

**Behavior:**

If no matching node is found:
  - The pod stays in Pending state.
  - It won’t fall back to any other node.

## Node Affinity

### What is Node Affinity in Kubernetes?

Node Affinity is a more flexible and powerful alternative to nodeSelector used to control where a pod is scheduled based on node labels.
It allows expressions, preferred rules, and multiple match conditions.

**Use Cases:**

  - Schedule pods based on region, zone, or hardware type.
  - Prefer certain nodes but still allow fallback.
  - Advanced scheduling for HA, cost, or performance optimization.

**Node Affinity Types:**

| Type                                              | Behavior                                         | Optional?  |
| ------------------------------------------------- | ------------------------------------------------ | ---------- |
| `requiredDuringSchedulingIgnoredDuringExecution`  | **Hard rule**: must match to be scheduled        | ❌ Required |
| `preferredDuringSchedulingIgnoredDuringExecution` | **Soft rule**: prefer to match, but not required | ✅ Optional |


### 1. Hard Node Affinity Example:

```
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: zone
            operator: In
            values:
            - us-east-1a
```
`Pod will only be scheduled on nodes where zone=us-east-1a.`

### 2. Soft (Preferred) Node Affinity Example:

```
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        preference:
          matchExpressions:
          - key: instance-type
            operator: In
            values:
            - spot
```
`Pod will prefer nodes with instance-type=spot, but can fall back if not available.`

## Taints and Tolerations vs Node Selector vs Node Affinity:

| Feature                  | **Taints & Tolerations**                | **Node Selector**                 | **Node Affinity**                            |
| ------------------------ | --------------------------------------- | --------------------------------- | -------------------------------------------- |
| **Purpose**              | Reject pods from nodes unless tolerated | Only allow pods on matching nodes | Match or prefer nodes based on labels        |
| **Applies to**           | Nodes (taints) + Pods (tolerations)     | Pods only                         | Pods only                                    |
| **Default behavior**     | Exclude by default                      | Include if match only             | Include or prefer based on rules             |
| **Pod control**          | ✅ Tolerates taints to allow scheduling  | ✅ Specifies required node labels  | ✅ Full control with required/preferred rules |
| **Supports expressions** | ❌ No                                    | ❌ No                              | ✅ Yes (In, NotIn, Exists, etc.)              |
| **Supports soft rules**  | ✅ via `PreferNoSchedule`                | ❌ No                              | ✅ Yes (`preferredDuringScheduling...`)       |
| **Eviction support**     | ✅ `NoExecute` effect                    | ❌ No                              | ❌ No                                         |
| **Complexity**           | ⚠️ Medium                               | ✅ Simple                          | ⚠️ Advanced                                  |


