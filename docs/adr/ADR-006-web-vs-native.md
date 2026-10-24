# ADR-006: Web app vs native companion for higher-assurance vault

- **Status:** Accepted
- **Date:** 2026-10-24
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** Product owner

## Context

§5.6: on the web, the operator delivers the JS, so a malicious build is a residual risk.

## PRD default position

> Web MVP with a hardened origin. Evaluate a signed native/PWA companion for stronger assurance.

## Options under consideration

- Web only, with signed and reproducible builds
- PWA with a pinned service worker + release manifest check
- Native companion (Tauri, which reuses `vault-core`) for recovery-critical actions

## Decision

Ship a static Svelte web MVP with isolated builds and hardened origins. Keep `vault-core` platform-neutral, but defer native and PWA companions until a demonstrated security or product need justifies them. The web-delivered build remains a residual risk; use signed, reproducible or verifiable releases and the vault-origin controls in the project invariants.

## Consequences / dependencies

- Revisit a native/PWA companion only through a new security-reviewed decision.
