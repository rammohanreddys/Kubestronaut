# K8S - Application LifeCycle Management:

Kubernetes provides tools and concepts to manage the entire lifecycle of a containerized application — from deployment to deletion, including scaling, upgrades, health checking, and rollback.

Here’s a breakdown of the full application lifecycle in Kubernetes:

## 1.  Deploy the Application:

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


## 2. Update & Roll Out New Versions:

Use rolling updates to deploy new versions without downtime.

```
kubectl set image deployment/my-app my-container=my-image:v2
```
