# K8S - Application LifeCycle Management:

Kubernetes provides tools and concepts to manage the entire lifecycle of a containerized application — from deployment to deletion, including scaling, upgrades, health checking, and rollback.

Here’s a breakdown of the full application lifecycle in Kubernetes:

## 1. Deploy the Application:

Deploy your app using YAML manifests or tools like kubectl, Helm, or Kustomize.

Common resources:
- Deployment or StatefulSet: for managing pods
- Service: for networking access
- ConfigMap/Secret: for configuration
- PersistentVolumeClaim: for storage, if needed

```
kubectl apply -f deployment.yaml
```
 ### Kubernetes Deployment Strategies:

Kubernetes supports multiple deployment strategies to roll out changes to your applications. These strategies help manage downtime, risk, and user experience when upgrading apps in production.

 #### 1. Recreate Strategy:

`Stops old pods first, then starts new ones.`
```
strategy:
  type: Recreate
```
Use When:
- You can't run two versions simultaneously (e.g., DB migrations).
- Downtime is acceptable.

#### 2. Rolling Update Strategy (⚙️ Default):

Gradually replaces old pods with new ones, avoiding downtime.
```
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 1
    maxSurge: 1
```

| Field            | Description                          |
| ---------------- | ------------------------------------ |
| `maxUnavailable` | Max old pods that can be unavailable |
| `maxSurge`       | Max extra pods beyond desired count  |

Use When:
- You want zero downtime.
- Your app supports running multiple versions simultaneously.

#### 3. Blue-Green Deployment (manual via labels or services):

`Deploy new version in parallel, then switch traffic.`

Steps:
- Deploy v2 as a separate Deployment (e.g., app-blue, app-green)
- Test app-green independently
- Change the Service selector to point to app-green

Use When:
- You want easy rollback by switching back to old version.
- You can afford running double resources temporarily.

#### 4. Canary Deployment (manual or with progressive delivery tools):

`Gradually expose new version to a small subset of users.`

Steps:
- Deploy new version with fewer replicas (e.g., 1 out of 10)
- Monitor performance and errors
- Gradually increase traffic by adjusting replica count or weight

Use When:
- You want to test in production with minimal risk.
- You use tools like Istio, Linkerd, Flagger, or Argo Rollouts.

#### 5. A/B Testing (Traffic Splitting)

Route traffic to different versions based on headers, user IDs, etc.
- Requires a service mesh like Istio or NGINX Ingress Controller.
- Can be combined with Canary or Blue-Green.

#### 6. Argo Rollouts (Advanced Strategy Controller)

Argo Rollouts is a Kubernetes controller to support:
- Canary deployments with automated analysis
- Blue-green strategies with gates
- Progressive delivery

```
apiVersion: argoproj.io/v1alpha1
kind: Rollout
...
spec:
  strategy:
    canary:
      steps:
      - setWeight: 20
      - pause: {duration: 30s}
      - setWeight: 50
      - pause: {duration: 60s}
```

#### Summary Comparison Table:

| Strategy       | Downtime | Parallel Versions | Rollback Ease | Tools Required       |
|:-------------- | -------- | ----------------- | ------------- |:-------------------- |
| Recreate       | ❌ Yes    | ❌ No              | ❌ Manual      | None (built-in)      |
| Rolling Update | ✅ No     | ✅ Yes             | ✅ With `undo` | None (built-in)      |
| Blue-Green     | ✅ No     | ✅ Yes             | ✅ Easy        | Manual or Argo       |
| Canary         | ✅ No     | ✅ Yes             | ✅ Controlled  | Manual / Argo / Mesh |
| A/B Testing    | ✅ No     | ✅ Yes             | ✅ Controlled  | Istio / NGINX        |


## 2. Update & Roll Out New Versions:

Use rolling updates to deploy new versions without downtime.

```
kubectl set image deployment/my-app my-container=my-image:v2
```

- Kubernetes gradually replaces old pods with new ones.
- Controlled using strategy.rollingUpdate in the Deployment spec.

## 3. Rollback to Previous Version:

If something goes wrong, rollback to a previous working version.
```
kubectl rollout undo deployment my-app
```

Track rollout history:
```
kubectl rollout history deployment my-app
```

## 4. Scaling the Application:

Scale manually or automatically based on usage.

Manual Scaling
```
kubectl scale deployment my-app --replicas=5
```

Auto Scaling using HPA (Horizontal Pod Autoscaler)
```
kubectl autoscale deployment my-app --cpu-percent=50 --min=2 --max=10
```

## 5. Health Checking

Use liveness and readiness probes to monitor pod health.
```
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 5
```

- Liveness: Restarts unhealthy pods.
- Readiness: Marks pods as ready for traffic.



















