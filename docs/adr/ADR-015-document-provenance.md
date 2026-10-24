# ADR-015: Document provenance verification sources

- **Status:** Accepted
- **Date:** 2026-10-24
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** Product owner

## Context

FR-5.6: provenance statuses must not overstate the evidence behind them.

## PRD default position

> Start with SELF_DECLARED/USER_UPLOADED. Add provider/official verification only with defined evidence and freshness rules.

## Options under consideration

- _None beyond the default._

## Decision

V1 supports only `SELF_DECLARED` and `USER_UPLOADED`. Add provider or official verification states only when the evidence source, verification procedure, freshness and expiry rules are defined and auditable.

## Consequences / dependencies

- Do not present a provenance label as stronger evidence than was collected.
