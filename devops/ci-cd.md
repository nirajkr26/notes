# CI/CD & Deployment — Interview Notes

## 1. CI/CD

### Continuous Integration

Developers integrate changes frequently and automated checks validate each change.

Typical pipeline:

```text
Commit / PR
   ↓
Lint
   ↓
Unit tests
   ↓
Build
   ↓
Security / dependency checks
   ↓
Integration tests
```

### Continuous Delivery

The software is kept in a releasable state and can be deployed through a controlled release process.

### Continuous Deployment

Every change that passes the required automated gates can be automatically deployed to production.

The distinction is important in interviews: **delivery means deployable; deployment means automatically deployed.**

## 2. Pipeline Design

A production pipeline often looks like:

```text
Developer
   ↓
Pull Request
   ↓
CI
 ┌──────────────┐
 │ lint          │
 │ unit tests    │
 │ build         │
 │ security      │
 └──────┬───────┘
        ↓
Artifact / Image
        ↓
Staging
        ↓
Integration / Smoke Tests
        ↓
Approval or automated promotion
        ↓
Production
        ↓
Monitoring + rollback
```

## 3. Build Once, Promote the Same Artifact

Avoid rebuilding the application separately for staging and production.

Prefer:

```text
Source
  ↓
Build once
  ↓
Immutable artifact
  ↓
Staging
  ↓
Production
```

This reduces the possibility that staging and production run different binaries/images.

## 4. Immutable Artifacts

Prefer immutable version identifiers such as:

```text
my-api:git-sha
my-api:2026-08-21-abc123
```

For high-assurance deployments, use image digests:

```text
image@sha256:...
```

Avoid production deployments that silently change because `latest` was overwritten.

## 5. Deployment Strategies

### Recreate

Stop old version, then start new version.

Simple but causes downtime unless additional infrastructure is used.

### Rolling

Gradually replace instances.

Good default for many stateless workloads.

### Blue/Green

Maintain two environments:

```text
Blue  → current
Green → new
```

Switch traffic after validation. Fast rollback, but requires additional capacity.

### Canary

Send a small percentage of traffic to the new version first.

```text
95% → v1
 5% → v2
```

Increase traffic when metrics remain healthy.

### Progressive delivery

Combine staged rollout with automated metrics analysis and rollback.

## 6. Rollback

A deployment strategy is incomplete without a rollback strategy.

Rollback triggers can include:

- Error-rate increase
- Latency SLO violation
- CrashLoopBackOff
- Failed smoke tests
- Business metric regression

Rollback should be faster and safer than attempting a large emergency fix during an incident.

## 7. Testing Pyramid

```text
             /\
            /  \
           / E2E\
          /------\
         /Integration\
        /------------\
       / Unit tests   \
      /----------------\
```

Many fast unit tests + targeted integration tests + a smaller number of high-value end-to-end tests is generally easier to maintain than relying entirely on E2E tests.

## 8. Secrets in CI/CD

Never place credentials directly in source code or committed workflow files.

Use:

- CI secret stores
- Cloud secret managers
- Short-lived credentials
- Workload identity/OIDC where supported

Prefer short-lived credentials over long-lived static cloud keys.

## 9. Supply Chain Security

Important controls include:

- Dependency pinning/lockfiles
- Dependency vulnerability scanning
- Container image scanning
- Minimal base images
- SBOM generation
- Artifact signing/verification
- Protected branches
- Least-privilege CI credentials
- Reproducible builds where practical

## 10. Infrastructure as Code

Infrastructure as Code (IaC) represents infrastructure configuration in version-controlled files.

Benefits:

- Reviewability
- Repeatability
- Auditability
- Reproducibility
- Reduced manual configuration drift

Common tools include Terraform, OpenTofu, CloudFormation and Pulumi.

The important concept is **declarative, reviewable infrastructure state**.

## 11. Configuration Management

Separate:

```text
Code
Configuration
Secrets
```

Configuration can vary by environment without rebuilding the application.

Secrets should have stronger controls and should not be treated as ordinary configuration.

## 12. GitHub Actions Example

```yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: npm

      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build
```

Treat version numbers in examples as illustrative; keep actions and runtimes updated according to the project's support policy.

## 13. GitOps

In a GitOps model, deployment configuration is declarative and stored in Git. A controller reconciles the cluster toward the desired state.

Advantages:

- Auditable changes
- Easy review
- Reproducibility
- Drift detection
- Clear rollback history

## 14. Deployment Safety

Before production:

```text
Automated tests
      ↓
Security checks
      ↓
Staging validation
      ↓
Health/readiness checks
      ↓
Progressive rollout
      ↓
Observe
      ↓
Promote or rollback
```

## 15. CI/CD Interview Questions

### Q1. CI vs CD?

CI continuously validates integrated changes. Continuous Delivery keeps changes ready for release; Continuous Deployment automatically releases validated changes to production.

### Q2. Why build once?

To ensure the exact artifact tested in one environment is promoted to later environments, reducing environment-specific build differences.

### Q3. Blue/green vs canary?

Blue/green switches traffic between complete environments. Canary gradually exposes a new version to a subset of traffic.

### Q4. How would you deploy a risky change?

Use a small canary, feature flag where appropriate, strong health checks, SLO-based monitoring, gradual promotion and a tested rollback path.

### Q5. Why are feature flags useful?

They separate deployment from feature activation, allowing code to be deployed without immediately exposing the behavior to all users.

### Q6. How do you secure CI/CD?

Use least-privilege credentials, short-lived authentication, protected branches, secret stores, dependency/image scanning, artifact integrity controls and restricted deployment permissions.
