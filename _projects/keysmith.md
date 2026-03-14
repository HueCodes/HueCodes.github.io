---
layout: post
title: "keysmith: Kubernetes Operator for Automated Secret Rotation"
---

Kubernetes operator that automates secret rotation on a cron schedule. Declare a `SecretRotationPolicy` CR, and keysmith handles credential generation, secret updates, workload restarts, and audit logging.

![keysmith architecture](/assets/images/projects/keysmith-architecture.svg)

## What it solves

SOC 2, PCI-DSS, and ISO 27001 all require periodic secret rotation. In practice, teams either rotate manually (error-prone, gets skipped) or build one-off CronJobs (no cluster state awareness, no audit trail, no failure handling). keysmith makes rotation a first-class Kubernetes resource with continuous reconciliation.

## How it works

A `SecretRotationPolicy` CRD defines:
- **Schedule**: Standard cron expression (5-field + shorthands like `@daily`)
- **Provider**: Which backend generates the new credential (AWS Secrets Manager, HashiCorp Vault, built-in `crypto/rand`)
- **Key mappings**: How provider keys map to Kubernetes Secret keys
- **Restart targets**: Deployments, StatefulSets, or DaemonSets to rolling-restart after rotation
- **Failure policy**: `Fail` (mark policy as failed) or `Ignore` (log and continue)

On each rotation, the controller:
1. Creates a `RotationRecord` in `Running` phase (audit trail starts before the attempt)
2. Calls the provider to fetch/generate new credentials
3. Updates the target Kubernetes Secret
4. Patches pod template annotations to trigger rolling restarts
5. Finalizes the `RotationRecord` as `Succeeded` or `Failed`
6. Dispatches HTTP webhooks on `RotationSucceeded`, `RotationFailed`, or `RotationSkipped`
7. Prunes old `RotationRecord`s beyond `historyLimit`

Manual rotation is supported via annotation: set `secrets.keysmith.io/rotate=now` and the controller rotates immediately. Policies can be suspended and resumed without deletion.

## Provider interface

The `Provider` interface is three methods: `Validate`, `FetchSecret`, `RotateSecret`. Adding a new backend is about 50 lines of Go. Register it in `main.go`, update the CRD enum, run `make manifests`.

Built-in providers:
- **static**: Generates cryptographically random passwords via `crypto/rand` (configurable length, 8-256 chars)
- **mock**: Returns deterministic versioned values for testing and demos
- **aws**: AWS Secrets Manager (stub, SDK integration documented inline)
- **vault**: HashiCorp Vault KV v2 (stub, client integration documented inline)

## Audit trail

`RotationRecord` is the compliance-critical piece. Every rotation attempt (success or failure) creates an immutable record capturing:
- Trigger source (scheduled vs. manual)
- Start time, completion time, duration
- Provider used
- Keys rotated
- Workloads restarted
- Error message on failure
- Retry count

Records are linked to policies via owner references and auto-pruned per `historyLimit`. Auditors can query the full rotation history for any secret.

## Admission webhooks

Defaulting and validation webhooks run at the API server:
- **Defaulting**: Sets `historyLimit=10` and `failurePolicy=Fail` if unset
- **Validation**: Parses cron expressions semantically (catches `60 * * * *`), requires `secretRef.name` and at least one key mapping

This catches configuration errors at apply time instead of at rotation time.

## Observability

Four Prometheus metrics:

| Metric | Type | Purpose |
|--------|------|---------|
| `keysmith_rotations_total` | Counter | Total rotation attempts by policy, provider, result |
| `keysmith_rotation_duration_seconds` | Histogram | Rotation execution latency |
| `keysmith_next_rotation_timestamp_seconds` | Gauge | Next scheduled rotation (alert on stale policies) |
| `keysmith_active_policies_total` | Gauge | Count of non-suspended policies |

OpenTelemetry tracing and structured logging via Zap round out the observability stack.

## Why an operator, not a CronJob

A CronJob runs a script and exits. It has no cluster state awareness, no status conditions, no leader election, no event reactivity. If a CronJob fails, you find out from missing logs. If a keysmith rotation fails, you get a `Failed` phase on the policy, a `RotationRecord` with the error, a webhook notification, and a Prometheus metric increment.

The operator pattern uses continuous reconciliation: if something drifts, the controller corrects it on the next loop. Leader election via Kubernetes Lease objects ensures only one replica runs rotations at a time.

## Testing

Ginkgo v2 + envtest (real kube-apiserver + etcd in-process). Tests cover:
- Scheduled and manual rotation happy paths
- Suspended policies
- Provider failure with both `Fail` and `Ignore` policies
- History pruning
- Missing restart targets (graceful degradation)

Coverage: 56%+ controller, 87-100% providers, 91% scheduler.

---

[View on GitHub](https://github.com/HueCodes/keysmith)
