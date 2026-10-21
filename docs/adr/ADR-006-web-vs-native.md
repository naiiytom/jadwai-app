# ADR-006: Web app vs native companion for higher-assurance vault

- **Status:** Proposed
- **Date:** 2026-10-21
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** _TBD (Architecture, Security)_

## Context

§5.6: on the web, the operator delivers the JS, so a malicious build is a residual risk.

## PRD default position

> Web MVP with a hardened origin. Evaluate a signed native/PWA companion for stronger assurance.

## Options under consideration

- Web only, with signed and reproducible builds
- PWA with a pinned service worker + release manifest check
- Native companion (Tauri, which reuses `vault-core`) for recovery-critical actions

## Proposed direction

Web MVP. Keep `vault-core` platform-neutral so a Tauri or mobile companion can reuse it.

## Consequences / dependencies

- _To be completed when the ADR is accepted._
