# AIP Mule Process Log Intelligence API - Specification

This repository is the contract source of truth for the `aip-mule-prc-log-intelligence-api` Process API.

## Purpose

Provide a stable orchestration contract that coordinates canonical log collection and analysis job dispatch to the control plane.

## Scope

- Define Process API endpoints for orchestration and job status retrieval.
- Standardise canonical payload fields, idempotency behaviour, and correlation traceability.
- Keep source-specific connector details out of the Process layer.

## Source of Truth

- Main contract file: `aip-mule-prc-log-intelligence-api.raml`
- Exchange descriptor: `exchange.json`

## Shared Governance Assets

This specification follows the shared governance baseline:

- Template baseline: `mule-utility-template-spec` `1.0.0`
- Common fragment reuse: `mule-utility-common-fragment` `1.0.0`
- Governance validation dependency: `anypoint-best-practices` `1.6.3`

## Repository Documents

- `docs/contract-overview.md`: orchestration design, canonical model, integration points, and failure strategy.
- `docs/changelog.md`: contract evolution history and compatibility notes.

## Ownership and Update Triggers

Update this repository when:

- orchestration resources or schema fields change,
- integration points with System APIs or control plane change,
- idempotency, retry, or security constraints are revised.

Breaking changes require explicit architecture approval and major version review.
