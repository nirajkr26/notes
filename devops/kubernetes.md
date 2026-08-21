# Kubernetes — Production & Interview Notes

> Kubernetes (K8s) is a container orchestration platform for deploying, scaling, networking and operating containerized workloads. These notes focus on concepts you should be able to explain in an interview and reason about during production troubleshooting.

## 1. Why Kubernetes?

Docker solves packaging and container execution. Kubernetes solves the larger operational problem:

```text
Many containers
     ↓
Scheduling + networking + service discovery
     ↓
Health checks + self-healing
     ↓
Rolling updates + rollbacks
     ↓
Horizontal scaling
     ↓
Declarative desired state
```

### Core capabilities

- Scheduling containers onto nodes
- Service discovery and load balancing
- Restarting failed containers
- Replacing unhealthy instances
- Rolling deployments and rollbacks
- Horizontal scaling
- Configuration and secret management
- Persistent storage abstraction
- Resource requests and limits
- Extensibility through controllers and CRDs

## 2. Architecture

```text
                     Control Plane
        ┌─────────────────────────────────┐
        │ API Server                      │
        │ Scheduler                       │
        │ Controller Manager              │
        │ etcd                            │
        └───────────────┬─────────────────┘
                        │
              Kubernetes API
                        │
        ┌───────────────┴─────────────────┐
        │             Worker Nodes        │
        │                                 │
        │ kubelet + container runtime     │
        │ kube-proxy / networking         │
        │ Pods                            │
        └─────────────────────────────────┘
```

### Components

| Component | Responsibility |
|---|---|
| `kube-apiserver` | Front door to the Kubernetes API; validates and persists API objects |
| `etcd` | Consistent key-value store holding cluster state |
| `kube-scheduler` | Chooses a suitable node for unscheduled Pods |
| `kube-controller-manager` | Runs controllers that reconcile desired and actual state |
| `kubelet` | Node agent that ensures Pods/containers are running as specified |
| Container runtime | Runs containers, commonly through CRI-compatible runtimes |
| `kube-proxy` | Implements service networking behavior on nodes in common deployments |

## 3. Declarative Model

Kubernetes is fundamentally declarative.

Instead of saying:

```text
Start three containers.
If one dies, start another.
Expose them through this IP.
```

you declare:

```yaml
spec:
  replicas: 3
```

Controllers continuously work toward the desired state.

This is the **reconciliation loop**:

```text
Desired state
     ↓
Compare with actual state
     ↓
Take corrective action
     ↓
Observe new state
     ↓
Repeat
```

## 4. Pods

A **Pod** is the smallest deployable unit in Kubernetes.

A Pod can contain one or more containers that share:

- Network namespace/IP
- Port space
- Volumes
- Pod lifecycle

Most applications use one main application container per Pod, adding sidecars only when there is a clear operational reason.

### Pod lifecycle

Typical phases include:

- `Pending`
- `Running`
- `Succeeded`
- `Failed`
- `Unknown`

Container restart behavior is controlled by the Pod's restart policy and higher-level controllers.

## 5. Deployment

A Deployment manages a replicated application and normally creates ReplicaSets, which create Pods.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
        - name: api
          image: example/api:1.0.0
          ports:
            - containerPort: 3000
          resources:
            requests:
              cpu: "100m"
              memory: "128Mi"
            limits:
              cpu: "500m"
              memory: "512Mi"
          readinessProbe:
            httpGet:
              path: /ready
              port: 3000
          livenessProbe:
            httpGet:
              path: /health
              port: 3000
```

### Deployment strategy

Default rolling updates gradually replace old Pods with new ones.

Useful settings:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 0
    maxSurge: 1
```

### Rollback

```bash
kubectl rollout status deployment/api
kubectl rollout history deployment/api
kubectl rollout undo deployment/api
```

## 6. ReplicaSet

A ReplicaSet maintains a desired number of matching Pods.

Normally you manage ReplicaSets indirectly through Deployments rather than creating them directly.

## 7. Services

Pods are ephemeral. Their IPs can change. A Service provides a stable networking abstraction.

### Service types

| Type | Purpose |
|---|---|
| `ClusterIP` | Internal cluster access; default |
| `NodePort` | Exposes service through a port on nodes |
| `LoadBalancer` | Integrates with an external/cloud load balancer |
| `ExternalName` | DNS alias to an external name |

```yaml
apiVersion: v1
kind: Service
metadata:
  name: api
spec:
  selector:
    app: api
  ports:
    - port: 80
      targetPort: 3000
  type: ClusterIP
```

## 8. Ingress

Ingress provides HTTP/HTTPS routing into cluster Services. An Ingress resource requires an Ingress controller to implement the routing behavior.

Typical routing:

```text
api.example.com/users  → users-service
api.example.com/orders → orders-service
```

For modern Kubernetes environments, also understand the **Gateway API** as the newer, more expressive networking API family for many use cases.

## 9. ConfigMaps and Secrets

### ConfigMap

Use for non-sensitive configuration.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  LOG_LEVEL: info
  PORT: "3000"
```

### Secret

Use for sensitive values such as credentials and tokens. Kubernetes Secret objects are not automatically equivalent to a fully secure external secret-management system; consider encryption at rest, RBAC, and external secret stores.

Never commit real credentials into Git.

## 10. Namespaces

Namespaces provide logical isolation within a cluster.

Common examples:

```text
production
staging
monitoring
logging
```

Namespaces help with organization, RBAC and resource quotas, but are **not** a complete security boundary by themselves.

## 11. Labels and Selectors

Labels attach metadata:

```yaml
labels:
  app: payments
  environment: production
  version: v2
```

Selectors use labels to identify objects.

This mechanism connects Deployments, Services, monitoring systems and other controllers to workloads.

## 12. Health Probes

### Liveness

Answers:

> Is this container still functioning well enough to keep running?

A failed liveness probe can cause a restart.

### Readiness

Answers:

> Should this Pod receive traffic right now?

A failed readiness probe removes the Pod from normal Service endpoints without necessarily restarting it.

### Startup

Useful for slow-starting applications. It prevents liveness/readiness checks from killing the application before initialization completes.

### Common mistake

Do not make liveness checks depend on every downstream dependency. A temporary database outage should not necessarily cause every application process to restart.

## 13. Requests and Limits

```yaml
resources:
  requests:
    cpu: "250m"
    memory: "256Mi"
  limits:
    cpu: "1"
    memory: "1Gi"
```

- **Request:** scheduling/resource reservation signal.
- **Limit:** upper resource constraint enforced according to the resource/runtime semantics.

CPU is measured in cores; `100m` means 0.1 CPU.

Memory is expressed in bytes using units such as `Mi` and `Gi`.

Bad resource settings can cause either waste or instability. Tune them using workload measurements rather than guessing.

## 14. Horizontal Pod Autoscaler

HPA adjusts the number of replicas based on metrics.

Typical flow:

```text
Traffic/load
    ↓
Metrics
    ↓
HPA
    ↓
Replica count
    ↓
Scheduler
    ↓
New Pods
```

CPU utilization is common, but production systems can scale on other metrics when metrics infrastructure supports them.

## 15. Scheduling

The scheduler considers constraints such as:

- Resource requests
- Node availability
- Node selectors
- Affinity/anti-affinity
- Taints and tolerations
- Topology constraints
- Scheduling policies

### Taints and tolerations

A taint repels Pods from a node unless a Pod has a matching toleration.

Useful for dedicated nodes such as GPU or infrastructure nodes.

### Affinity

Affinity expresses preferences or requirements about where workloads should run.

## 16. Persistent Storage

Containers are ephemeral, but applications such as databases need durable storage.

Kubernetes abstracts storage using:

```text
StorageClass
     ↓
PersistentVolumeClaim (PVC)
     ↓
PersistentVolume (PV)
     ↓
Storage backend
```

### PVC example

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-data
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

For stateful applications, understand **StatefulSet**, stable identities, ordered behavior and persistent volume claims.

## 17. StatefulSet vs Deployment

| Deployment | StatefulSet |
|---|---|
| Stateless workloads | Stateful workloads |
| Pods are interchangeable | Pods have stable identities |
| Usually interchangeable storage | Stable persistent storage association |
| Web/API services | Databases, brokers, clustered stateful systems |

Kubernetes does not magically make a database distributed or safe. Stateful systems still require application-level replication, backup and recovery design.

## 18. DaemonSet and Job

### DaemonSet

Runs a Pod on eligible nodes.

Use cases:

- Node-level log collectors
- Monitoring agents
- Security agents
- Storage/network plugins

### Job

Runs work until successful completion.

### CronJob

Schedules Jobs periodically.

## 19. RBAC

Role-Based Access Control controls who can perform which Kubernetes API actions.

Key objects:

- Role
- ClusterRole
- RoleBinding
- ClusterRoleBinding
- ServiceAccount

Principle:

> Give each workload and human only the permissions it needs.

Avoid broad `cluster-admin` access for normal applications.

## 20. Network Policies

NetworkPolicy can restrict which Pods may communicate with which other Pods, depending on cluster network implementation.

Think in terms of:

```text
frontend → API      allowed
API      → database allowed
frontend → database denied
```

Default-deny plus explicit allow rules is often a stronger starting security model than allowing everything.

## 21. Security Checklist

- Run containers as non-root where possible.
- Use minimal, trusted base images.
- Scan images for vulnerabilities.
- Pin or otherwise control image versions; avoid relying on `latest` for production deployments.
- Apply RBAC least privilege.
- Protect Secrets and encrypt sensitive data at rest.
- Use NetworkPolicies where supported.
- Set resource requests/limits.
- Use admission/policy controls where appropriate.
- Keep Kubernetes and node components patched.
- Restrict access to the API server.
- Sign/verify artifacts where the supply-chain model requires it.

## 22. Essential kubectl Commands

```bash
kubectl get nodes
kubectl get pods
kubectl get pods -A
kubectl get pods -o wide
kubectl get deployments
kubectl get services
kubectl get ingress
kubectl get events --sort-by=.lastTimestamp

kubectl describe pod <pod>
kubectl describe deployment <deployment>
kubectl logs <pod>
kubectl logs <pod> -c <container>
kubectl logs -f <pod>
kubectl logs <pod> --previous

kubectl exec -it <pod> -- sh
kubectl port-forward svc/api 8080:80
kubectl rollout status deployment/api
kubectl rollout restart deployment/api
kubectl rollout undo deployment/api

kubectl apply -f deployment.yaml
kubectl delete -f deployment.yaml
kubectl diff -f deployment.yaml
```

## 23. Troubleshooting Workflow

### Pod is Pending

Check:

```bash
kubectl describe pod <pod>
kubectl get events --sort-by=.lastTimestamp
```

Common causes:

- Insufficient resources
- Unsatisfied node affinity
- Taints without tolerations
- PVC not bound
- Scheduling constraints

### Pod is CrashLoopBackOff

```bash
kubectl logs <pod>
kubectl logs <pod> --previous
kubectl describe pod <pod>
```

Investigate:

- Application crash
- Bad configuration
- Missing Secret/ConfigMap
- Wrong command/arguments
- Failed dependency
- Probe failures
- OOM kill

### Service has no traffic

Check:

```bash
kubectl get svc <service>
kubectl get endpointslice
kubectl get pods --show-labels
kubectl describe svc <service>
```

Usually investigate selector mismatch, readiness, ports and network policy.

### ImagePullBackOff

Check:

- Image name/tag
- Registry availability
- ImagePullSecrets
- Registry permissions
- Network/DNS

### OOMKilled

Likely memory usage exceeded the effective container memory limit. Investigate application memory behavior and whether requests/limits reflect actual workload needs.

## 24. Kubernetes Deployment Pipeline

A production flow might be:

```text
Git push
  ↓
CI: lint + unit tests + security checks
  ↓
Build container image
  ↓
Scan image
  ↓
Push immutable image tag/digest
  ↓
Deploy to staging
  ↓
Smoke/integration tests
  ↓
Progressive production rollout
  ↓
Observe metrics/logs/traces
  ↓
Promote or rollback
```

## 25. GitOps

GitOps treats Git as the declarative source of truth for desired infrastructure/application state.

Typical model:

```text
Developer
   ↓
Git repository
   ↓
CI validates/builds artifact
   ↓
GitOps controller observes desired state
   ↓
Cluster reconciles
```

Popular tooling includes Argo CD and Flux. The important interview concept is **declarative desired state + continuous reconciliation**, not memorizing one vendor.

## 26. Kubernetes Interview Questions

### Q1. Why use Kubernetes if Docker already exists?

Docker primarily packages and runs containers. Kubernetes orchestrates containers across machines and provides scheduling, service discovery, self-healing, rolling deployments, scaling and declarative reconciliation.

### Q2. Pod vs container?

A container is a process/runtime unit. A Pod is Kubernetes' smallest deployable unit and can contain one or more tightly coupled containers sharing networking and volumes.

### Q3. Deployment vs Service?

Deployment manages workload replicas and rollout. Service provides stable network access to matching Pods.

### Q4. Liveness vs readiness?

Liveness answers whether the process should be restarted. Readiness answers whether it should receive traffic.

### Q5. What happens when a Pod dies?

If managed by a controller such as a Deployment, Kubernetes observes the actual replica count and creates a replacement to restore the desired state.

### Q6. Why is Kubernetes called declarative?

You specify desired state in API objects, and controllers continuously reconcile actual state toward that desired state.

### Q7. What is etcd?

The strongly consistent key-value store used by Kubernetes to persist cluster API state.

### Q8. What is CrashLoopBackOff?

It indicates a container is repeatedly failing and Kubernetes is backing off before restarting it. Inspect current/previous logs, Pod events, probes, configuration and resource limits.

### Q9. How do you debug a Service that returns no response?

Verify Service selector → EndpointSlices → Pod readiness → target port → network policy → application listener → ingress/load balancer path.

### Q10. How do you safely deploy a new version?

Use immutable image references, automated tests, readiness checks, rolling/progressive rollout, monitoring and an explicit rollback strategy.
