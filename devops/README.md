# DevOps — Production & Interview Preparation

> A complete DevOps path from containers and cloud infrastructure to Kubernetes, CI/CD and production observability.

## Learning Path

```text
Linux / Networking fundamentals
          ↓
        Docker
          ↓
     Docker Compose
          ↓
       CI / CD
          ↓
 Infrastructure as Code
          ↓
     Cloud platforms
          ↓
      Kubernetes
          ↓
 Monitoring + Alerting
          ↓
 Logging
          ↓
 Metrics + Prometheus
          ↓
 Grafana dashboards
          ↓
 Traces + OpenTelemetry
          ↓
 SLOs / Incident Response
          ↓
 Security / Reliability
```

## Notes

| File | Focus |
|---|---|
| [Docker](docker.md) | Images, containers, Dockerfiles, networking, volumes, Compose, security, troubleshooting |
| [Cloud Computing](cloudcomputing.md) | IaaS/PaaS/SaaS, AWS/Azure/GCP, networking, IAM, storage, serverless, IaC, security, cost |
| [CI/CD](ci-cd.md) | Continuous integration/delivery, pipelines, artifacts, deployments, GitOps, supply chain security |
| [Kubernetes](kubernetes.md) | Pods, Deployments, Services, Ingress/Gateway, ConfigMaps, Secrets, probes, HPA, storage, RBAC, networking, troubleshooting |
| [Observability](observability.md) | Metrics, logs, traces, Prometheus, PromQL, Grafana, Alertmanager, Loki, OpenTelemetry, SLOs, incidents |

## Production Mental Model

A good DevOps engineer thinks about the entire lifecycle:

```text
Code
 ↓
Build
 ↓
Test
 ↓
Package
 ↓
Secure
 ↓
Deploy
 ↓
Run
 ↓
Observe
 ↓
Respond
 ↓
Improve
```

The objective is not simply "deploy successfully." The objective is to make software **repeatable to deploy, safe to operate, observable, recoverable and continuously improvable**.

## DevOps Interview Checklist

### Containers

- Image vs container
- Layers and caching
- Multi-stage builds
- Namespaces and cgroups
- Volumes
- Container networking
- Security and non-root containers

### CI/CD

- CI vs Continuous Delivery vs Continuous Deployment
- Build once, promote artifact
- Immutable images
- Blue/green vs canary vs rolling
- Rollback
- Feature flags
- Secrets in pipelines
- Supply-chain security
- GitOps

### Kubernetes

- Control plane architecture
- Pod vs container
- Deployment vs ReplicaSet vs Service
- Service types
- Ingress and Gateway API
- ConfigMaps and Secrets
- Readiness/liveness/startup probes
- Requests and limits
- HPA
- StatefulSet/DaemonSet/Job/CronJob
- RBAC
- NetworkPolicy
- Storage/PV/PVC/StorageClass
- Scheduling, affinity, taints/tolerations
- CrashLoopBackOff/ImagePullBackOff/Pending troubleshooting

### Observability

- Monitoring vs observability
- Metrics/logs/traces
- Golden signals
- RED / USE
- Prometheus data model
- Counter/gauge/histogram/summary
- PromQL
- Exporters
- Grafana dashboards
- Alertmanager
- Structured logging
- Loki
- OpenTelemetry
- Distributed tracing
- SLI/SLO/SLA
- Error budgets
- Incident response

## Production Troubleshooting Framework

When a service is failing:

```text
1. What is the user impact?
2. When did it start?
3. What changed recently?
4. Is the problem global or isolated?
5. What do rate, errors, latency and saturation show?
6. Which dependency is slow/failing?
7. What do traces show?
8. What do logs show?
9. Can we mitigate safely?
10. How do we prevent recurrence?
```

**Mitigate first when necessary; investigate root cause after the immediate impact is controlled.**

## Key Principle

> **If you cannot observe it, debug it, roll it back, or recover it, it is not production-ready.**
