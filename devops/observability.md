# Monitoring, Logging & Observability — Prometheus, Grafana & OpenTelemetry

> Production systems should not only run; they should be understandable when something goes wrong. This guide covers metrics, logs, traces, alerting, dashboards, SLOs and the common Prometheus/Grafana/OpenTelemetry ecosystem.

## 1. Monitoring vs Observability

**Monitoring** asks:

> Is the system behaving within expected bounds?

**Observability** asks:

> Can we understand why the system is behaving this way from the data it exposes?

Monitoring is largely about known failure modes and expected signals. Observability emphasizes the ability to investigate unknown or complex failure modes.

## 2. The Three Common Signals

```text
                 Observability
                      │
        ┌─────────────┼─────────────┐
        │             │             │
      Metrics        Logs         Traces
        │             │             │
   numbers over    events/text    request path
      time                       across services
```

### Metrics

Numeric measurements sampled over time.

Examples:

- Request rate
- Error rate
- CPU usage
- Memory usage
- Latency
- Queue depth

### Logs

Discrete events emitted by applications and infrastructure.

Examples:

```text
INFO  request completed request_id=abc duration_ms=42
WARN  retrying database connection attempt=2
ERROR payment failed order_id=123 reason=timeout
```

### Traces

A distributed trace follows one logical request across services. It consists of spans.

```text
Trace
 ├── API span
 │    ├── DB span
 │    └── cache span
 └── payment-service span
      └── external-provider span
```

## 3. Golden Signals

A useful starting framework for services is:

1. **Latency** — how long requests take.
2. **Traffic** — how much demand exists.
3. **Errors** — how many requests fail.
4. **Saturation** — how close resources are to capacity.

Do not monitor only CPU. A service can have normal CPU while returning 30% errors because of a database or downstream dependency.

## 4. RED and USE

### RED — request-oriented services

- **Rate**
- **Errors**
- **Duration**

### USE — infrastructure/resource-oriented systems

- **Utilization**
- **Saturation**
- **Errors**

These frameworks help prevent dashboards from becoming collections of arbitrary charts.

## 5. Prometheus

Prometheus is a metrics monitoring and alerting ecosystem centered around a time-series database and a PromQL query language.

Typical architecture:

```text
Application / Exporter
        ↓
   /metrics endpoint
        ↓
    Prometheus
        ↓
      PromQL
    ↙       ↘
Dashboard   Alerting
    ↓          ↓
 Grafana    Alertmanager
```

Prometheus commonly uses a pull/scrape model for collecting metrics from targets.

## 6. Prometheus Data Model

A time series is identified by a metric name plus a set of labels.

Conceptually:

```text
http_requests_total{
  method="GET",
  route="/users",
  status="200"
}
```

Labels are powerful, but unbounded/high-cardinality labels can create serious memory and query-cost problems.

### Avoid labels such as

```text
user_id
request_id
random_uuid
full_url_with_query_string
```

unless there is a deliberate cardinality strategy.

## 7. Metric Types

Prometheus client libraries commonly expose:

### Counter

Monotonically increasing value, except when the process restarts.

Examples:

- Total requests
- Total errors
- Total jobs processed

Query:

```promql
rate(http_requests_total[5m])
```

### Gauge

Can increase or decrease.

Examples:

- Active connections
- Queue size
- Temperature
- Memory usage

### Histogram

Samples observations into configurable buckets and exposes count/sum/bucket information. Useful for latency distributions and percentile estimation with appropriate bucket design.

Example:

```promql
histogram_quantile(
  0.95,
  sum by (le) (rate(http_request_duration_seconds_bucket[5m]))
)
```

### Summary

Calculates quantiles client-side in many client libraries. Summaries and histograms have different aggregation characteristics; histograms are generally more useful when you need aggregation across instances.

## 8. PromQL Essentials

### Select a metric

```promql
http_requests_total
```

### Filter labels

```promql
http_requests_total{status="500"}
```

### Rate of a counter

```promql
rate(http_requests_total[5m])
```

### Sum by label

```promql
sum by (service) (rate(http_requests_total[5m]))
```

### Error rate

```promql
sum(rate(http_requests_total{status=~"5.."}[5m]))
/
sum(rate(http_requests_total[5m]))
```

### Average latency approximation from histogram sum/count

```promql
sum(rate(http_request_duration_seconds_sum[5m]))
/
sum(rate(http_request_duration_seconds_count[5m]))
```

## 9. Exporters

An exporter translates metrics from another system into a format Prometheus can scrape.

Examples:

- Node Exporter → host metrics
- cAdvisor → container metrics
- Database-specific exporters
- Blackbox Exporter → endpoint/probe metrics

The key concept is **instrumentation/collection adapter**, not memorizing every exporter.

## 10. Grafana

Grafana is a visualization and observability platform used to query, visualize and correlate data from multiple sources.

Typical sources include:

- Prometheus
- Loki
- Elasticsearch/OpenSearch
- Tempo
- Cloud-provider monitoring systems
- SQL databases

### Good dashboard structure

```text
Service Overview
 ├── Availability / SLO
 ├── Request rate
 ├── Error rate
 ├── p50 / p95 / p99 latency
 ├── Saturation
 ├── Dependency health
 └── Recent incidents / annotations
```

A dashboard should help answer a question. Avoid dozens of charts with no operational purpose.

## 11. Alerting

An alert should represent a condition requiring action, not merely an interesting metric.

Bad:

> CPU is above 70%.

Better:

> The service is approaching sustained capacity and user-facing latency/error objectives are degrading.

### Alert design principles

- Alert on symptoms where possible.
- Include useful context.
- Link to a runbook.
- Avoid excessive sensitivity.
- Avoid duplicate alerts for the same incident.
- Define ownership.
- Test alert delivery.

## 12. Alertmanager

Prometheus commonly integrates with Alertmanager for alert handling.

Alertmanager can support:

- Grouping
- Deduplication
- Routing
- Silencing
- Inhibition
- Notification integrations

Conceptually:

```text
Prometheus rule
      ↓
 Alertmanager
  ↙    ↓     ↘
Email  Slack  Pager
```

## 13. Recording Rules

Recording rules precompute frequently used PromQL expressions and store their results as new time series.

Useful when:

- Queries are expensive.
- Dashboards repeatedly execute the same expression.
- SLO calculations are reused.

## 14. Logs

### Structured logging

Prefer machine-readable structured events:

```json
{
  "level": "error",
  "service": "payments",
  "event": "charge_failed",
  "request_id": "abc123",
  "error_type": "timeout"
}
```

Benefits:

- Searchability
- Aggregation
- Parsing
- Correlation
- Automated analysis

### Log levels

Typical levels:

```text
DEBUG
INFO
WARN
ERROR
```

Use levels intentionally. Logging everything at `ERROR` destroys the signal-to-noise ratio.

## 15. Logging Architecture

A common Kubernetes architecture is:

```text
Application stdout/stderr
          ↓
      Node collector
          ↓
     Log backend
          ↓
       Grafana
```

Popular components include Fluent Bit, Fluentd, Vector, Filebeat, Loki and Elasticsearch/OpenSearch depending on the architecture.

## 16. Loki

Grafana Loki is a log aggregation system designed around labels and log streams rather than indexing every log field like a traditional full-text log database.

A common stack is:

```text
Prometheus → metrics
Loki      → logs
Tempo     → traces
Grafana   → visualization/correlation
```

## 17. OpenTelemetry

OpenTelemetry is a vendor-neutral observability framework for generating, collecting and exporting telemetry such as metrics, logs and traces.

It helps avoid tightly coupling application instrumentation to a single monitoring vendor.

Typical flow:

```text
Application
    ↓
OpenTelemetry SDK / instrumentation
    ↓
OpenTelemetry Collector
    ↓
Backend(s)
 ┌──────┬──────┬──────┐
Metrics Logs  Traces
```

## 18. Tracing Concepts

### Trace

Represents one end-to-end operation.

### Span

Represents one unit of work inside a trace.

Important span attributes may include:

- Service name
- Operation name
- Duration
- Status
- Selected request metadata
- Trace/span IDs

Never blindly attach secrets or sensitive user data to telemetry.

## 19. Trace Context Propagation

Distributed tracing requires the trace context to move between services.

```text
Browser
  ↓ trace context
API Gateway
  ↓
User Service
  ↓
Payment Service
  ↓
Database / external API
```

Without propagation, each service may produce disconnected traces.

## 20. Correlating Metrics, Logs and Traces

The real value of observability appears when signals connect.

Example incident:

```text
Metric:
p95 latency increased
        ↓
Trace:
most latency occurs in payment-service
        ↓
Logs:
payment-service shows database timeout errors
        ↓
Infrastructure metrics:
database connection pool saturated
```

This is far more useful than looking at four unrelated dashboards.

## 21. SLI, SLO and SLA

### SLI

A measured indicator of service behavior.

Example:

```text
successful requests / total valid requests
```

### SLO

The target for an SLI.

Example:

```text
99.9% of valid requests succeed over a rolling window.
```

### SLA

A formal agreement with customers/business, often containing consequences for failing commitments.

### Error budget

If the SLO is 99.9%, the allowed unreliability is approximately 0.1% over the chosen window.

The error budget helps balance reliability against release velocity.

## 22. Four Golden Signals + SLO Dashboard

For an API, a useful dashboard starts with:

```text
Availability / SLO
Request rate
Error rate
p50 / p95 / p99 latency
Saturation
Dependency failures
```

Then drill into:

```text
service → endpoint → instance/pod → dependency → trace → logs
```

## 23. Kubernetes Observability

Monitor at multiple layers:

### Cluster

- Node availability
- CPU/memory pressure
- Disk pressure
- API server health
- etcd health

### Workload

- Desired vs available replicas
- Restarts
- CPU/memory usage
- OOMKills
- Pending Pods
- Readiness failures

### Application

- Request rate
- Error rate
- Latency
- Business metrics

### Network

- DNS failures
- Connection errors
- Request latency
- Network policy drops where observable

## 24. Blackbox vs Whitebox Monitoring

### Whitebox

Inspect internal application/system metrics.

Examples:

- Queue length
- GC behavior
- DB pool usage
- Request latency

### Blackbox

Test the system from the outside.

Examples:

- HTTP availability probe
- DNS probe
- TCP connection probe

Use both. A system can look healthy internally while the user-facing endpoint is unavailable.

## 25. Alert Fatigue

Alert fatigue occurs when engineers receive too many low-value alerts and start ignoring them.

Reduce it by:

- Alerting on actionable conditions.
- Using multi-window/multi-burn-rate SLO approaches where appropriate.
- Grouping related alerts.
- Adding severity and ownership.
- Maintaining runbooks.
- Reviewing noisy alerts after incidents.

## 26. Incident Investigation Workflow

```text
1. Confirm impact
2. Identify affected service/region/tenant
3. Check recent deployments/config changes
4. Check golden signals
5. Compare healthy vs unhealthy instances
6. Follow dependency path
7. Correlate metrics → traces → logs
8. Mitigate first
9. Investigate root cause
10. Document and improve
```

Do not start by changing random production configuration. Preserve evidence where possible.

## 27. Common Failure Patterns

### High latency + normal CPU

Investigate:

- Database latency
- External API latency
- Connection pools
- Locks/contention
- Network
- Queueing

### High CPU + low traffic

Investigate:

- Runaway loop
- GC behavior
- Hot code path
- Unexpected background job
- Traffic measurement issue

### Errors only on one Pod

Compare:

- Pod image/version
- Node
- Environment/config
- Resource pressure
- Local dependency state

### All Pods fail after deployment

Strongly suspect:

- Bad image
- Bad configuration
- Secret changes
- Schema incompatibility
- Startup failure
- Dependency incompatibility

## 28. Interview Questions

### Q1. What is observability?

The ability to understand internal system behavior from externally emitted telemetry. Metrics, logs and traces are the common signals.

### Q2. Monitoring vs observability?

Monitoring tells you that a known condition is unhealthy; observability gives you enough context to investigate why a complex or previously unknown failure occurred.

### Q3. Why are high-cardinality Prometheus labels dangerous?

Each unique label combination can create another time series. Unbounded values such as user IDs can create huge numbers of series, increasing memory, storage and query cost.

### Q4. Counter vs gauge?

A counter represents a cumulative quantity that normally increases; a gauge represents a current value that can move in either direction.

### Q5. Why use `rate()` on counters?

Counters accumulate totals. `rate()` estimates their per-second increase over a time range, making the result useful for traffic/error-rate analysis.

### Q6. Histogram vs summary?

Histograms expose bucketed observations that can be aggregated across instances. Summaries generally calculate quantiles at the client. Choose based on aggregation and accuracy requirements.

### Q7. What makes a good alert?

It detects an actionable user/system problem, has appropriate sensitivity, identifies ownership, contains useful context and links to a runbook or remediation path.

### Q8. How would you debug a latency spike?

Start with scope and timing, check rate/errors/latency/saturation, compare affected vs healthy instances, inspect traces for slow spans, then correlate with logs and dependency metrics. Check recent deployments/configuration changes.

### Q9. Why is CPU-only monitoring insufficient?

Many failures are caused by latency, memory pressure, connection exhaustion, dependency failures, queue saturation or application errors while CPU remains normal.

### Q10. What is distributed tracing?

Tracing follows a logical request across service boundaries using a trace composed of spans, allowing engineers to identify where time and failures occur.

## 29. Production Checklist

Before calling a service production-ready, ask:

- Do we have request/error/latency metrics?
- Do we have structured logs?
- Can logs be correlated with requests/traces?
- Do we have health endpoints?
- Are readiness and liveness semantics correct?
- Do we have actionable alerts?
- Are SLOs defined for important user journeys?
- Is there a dashboard for on-call?
- Are deployment events visible?
- Can we identify the responsible owner?
- Do alerts have runbooks?
- Can we investigate historical incidents?
- Are telemetry costs and cardinality controlled?
- Are secrets and sensitive data excluded from telemetry?
