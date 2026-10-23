# services/ (planned)

| Dir | Service | Data domain |
|---|---|---|
| `identity/` | Accounts, passkeys/MFA, trusted devices, verifier IAL | identity |
| `vault-api/` | Ciphertext objects, revisions (CAS), attachments, provenance | vault |
| `recovery-orchestrator/` | Unlock state machine, approvals, disputes, release delay, Share A escrow | recovery |
| `notification/` | LINE/Email/push, heartbeat escalation | — (opaque refs only) |
| `lead-isolation/` | Consent receipts, Privacy-Limited leads | lead (**no vault IDs**) |
| `partner-registry/` | Agents, HR orgs, partners, license checks | partner |
| `security-events/` | Append-only, hash-chained event store | security |

Services never share DB schemas. They talk over APIs and the transactional outbox. See
[ARCHITECTURE §4](../docs/ARCHITECTURE.md#4-backend-services-logical).
