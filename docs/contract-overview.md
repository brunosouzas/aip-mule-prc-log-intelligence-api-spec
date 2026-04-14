# Contract Overview - AIP Process Log Intelligence API

## Context and Intent

This Process API orchestrates the log-intelligence workflow from source collection to control-plane analysis dispatch.

Design intent:

- keep orchestration and canonical mapping in the Process layer,
- keep source-specific connectivity in System APIs,
- keep AI job orchestration explicit and auditable with correlation and idempotency controls.

## Orchestration Design

Primary orchestration sequence:

1. Client submits `POST /analysis-jobs` with source list, filters, and idempotency key.
2. Process API validates request window, source set, and idempotency constraints.
3. Process API calls selected System APIs to collect canonical logs.
4. Process API normalises payload into canonical `CanonicalLogEntry[]`.
5. Process API dispatches `analysis.debug.requested.v1`-compatible payload to the control plane.
6. Process API returns `202` with orchestration summary and propagated `correlationId`.

Status retrieval:

- `GET /analysis-jobs/{jobId}` returns current lifecycle state and optional result URI.

## Canonical Payload Model

Core canonical record:

- `logId`, `timestamp`, `sourceSystem`, `serviceName`, `environment`, `severity`, `message`
- `correlationId` for distributed tracing
- `dataClass` and `redactionApplied` for trust-boundary control

Orchestration-level fields:

- `idempotencyKey` for deduplication,
- `sourceStatuses[]` with retry counts and per-source outcome,
- `dispatchEventName` and `dispatchRetryCount` for operational diagnostics.

## Integration Points

Upstream:

- Experience API consumers call Process API orchestration endpoints.

Downstream:

- `aip-mule-sys-anypoint-logs-api` System API for Anypoint source retrieval.
- `aip-mule-sys-observability-logs-api` System API for observability source retrieval.
- `aip-control-plane-go` for analysis job submission and status checks.

## Idempotency, Failure, and Retry Approach

Idempotency strategy:

- Require `idempotencyKey` in request payload (or header override),
- return existing accepted job metadata when a duplicate key is detected within retention window.

Retry strategy:

- Retry transient dependency failures only (System API connectivity/timeouts and control-plane unavailability).
- Keep retries bounded with explicit max attempts and interval.
- Mark source-level failures in `sourceStatuses`; allow partial collection when policy permits.

Failure categories:

- `validation`: malformed requests or unsupported source values,
- `dependency`: downstream System API/control-plane errors,
- `internal`: unexpected mapping or runtime errors.

## Non-Functional Expectations

- **Security**: no restricted or secret values in outbound control-plane payload.
- **Reliability**: explicit bounded retries and deterministic failure categories.
- **Observability**: correlation propagation and orchestration status metadata by contract.
- **Cost**: bounded filter windows and result limits prevent unbounded downstream calls.

## Evolution Rules

Update this document when orchestration resources, canonical mapping fields, integration endpoints, or retry/idempotency behaviour changes.

Breaking changes require architecture review and major contract versioning.
