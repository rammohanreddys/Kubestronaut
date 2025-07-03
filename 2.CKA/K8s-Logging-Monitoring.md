# K8S Logging and Monitoring:

In Kubernetes, logging is essential for troubleshooting, observability, and monitoring application and system behavior. Logging happens at multiple levels:

## Types of Logs in Kubernetes:

| Log Type             | Description                                                                          |
| -------------------- | ------------------------------------------------------------------------------------ |
| **Application logs** | Logs from your containerized app (e.g., `nginx`, `nodejs`, `java`)                   |
| **Container logs**   | Stdout/stderr of containers (managed by container runtime)                           |
| **Node logs**        | Logs from system components on a node (e.g., kubelet, kube-proxy)                    |
| **Cluster logs**     | Logs from control plane components (e.g., API server, scheduler, controller-manager) |

**Viewing Logs with kubectl:**

```
kubectl logs <pod-name>
```
**View logs of a pod with multiple containers:**
```
kubectl logs <pod-name> -c <container-name>
```

**Stream logs (like tail -f):**
```
kubectl logs -f <pod-name>
```

**View logs of a previous crashed container:**
```
kubectl logs --previous <pod-name>
```

## Log Storage and Retention:

By default:
- Logs are written to stdout/stderr and managed by the container runtime (e.g., containerd, Docker).
- On each node, logs are stored in:

However, logs do not persist if:
- The pod is deleted
- The node crashes and is unrecoverable

## Production-Grade Logging:

In production, logs should be centralized and aggregated using a logging stack:

Popular Logging Solutions:
| Stack                            | Components                                  |
| -------------------------------- | ------------------------------------------- |
| **EFK**                          | Elasticsearch + Fluentd/Fluent Bit + Kibana |
| **Loki stack**                   | Promtail/Fluent Bit + Loki + Grafana        |
| **ELK**                          | Elasticsearch + Logstash + Kibana           |
| **Datadog / Splunk / New Relic** | Cloud-native logging tools                  |

Logs are shipped using a DaemonSet agent (e.g., fluent-bit, promtail) running on each node.

Example: Fluent Bit DaemonSet
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluent-bit
  namespace: logging
spec:
  template:
    spec:
      containers:
      - name: fluent-bit
        image: fluent/fluent-bit:latest
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      volumes:
      - name: varlog
        hostPath:
          path: /var/log

```
`This setup reads node logs and forwards them to Elasticsearch, Loki, or any destination.`

## Best Practices for Logging:

- Send logs to stdout/stderr (not files inside the container)
- Use a centralized logging stack for storage/search
- Tag logs with metadata (pod name, namespace, container, node)
- Set log retention and rotation policies
- Avoid too much verbosity in logs (can bloat storage)

## To use top command along with kubectl:
```
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
kubectl top pods
kubectl top nodes
```
