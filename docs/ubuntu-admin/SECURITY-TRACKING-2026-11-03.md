# Ubuntu Admin WebApp — P0/P1 security tracking

Status is **pending independently verified source and test evidence** unless otherwise specified. Reported fixes in conversation must be compared with a concrete new archive/commit before closure.

## P0 — blocking
| ID | Concern | Required acceptance |
|---|---|---|
| P0-01 | install.sh side effects on shared host | Dry-run/preflight; no unrelated package/service mutation; rollback tests |
| P0-02 | restore.sh global php-fpm stop and non-transactional restore | Application-scoped maintenance; consistent SQLite/WAL snapshot; atomic replacement and failure rollback |
| P0-03 | project_user path and execution permissions | Canonical path validation, ownership isolation, symlink/traversal and cross-project denial tests |
| P0-04 | Worker restart orphan process | Kill/reap process tree, durable state reconciliation, crash/restart integration tests |
| P0-05 | root Broker timeout/lock release | Timeout, process-group termination, token replay and lock recovery tests |
| P0-06 | restore / WAL consistency and recovery | Injected failure during backup, replace, migration and restart; recovery drill |

## P1 — required before release
| ID | Concern | Required acceptance |
|---|---|---|
| P1-01 | idempotency request fingerprint | Identical replay returns same result; conflicting payload rejected |
| P1-02 | active_key version/test scope | Version and test-mode isolation under concurrent requests |
| P1-03 | cross-Task version association | Foreign version rejected at API and DB boundary |
| P1-04 | update affected rows = 0 | Explicit not-found/conflict response |
| P1-05 | JSON request validation | Content-Type, top-level object, malformed JSON and size tests |
| P1-06 | SQLite CHECK constraints | Invalid IDs rejected by actual SQLite engine |
| P1-07 | root token expiry | Expired, future-dated, >5-minute and replay tests |
| P1-08 | DB threat model | Permissions, secrets, WAL/SHM, backup, corruption and local-user access |
| P1-09 | subprocess cleanup | Forced timeout and crash tests with no orphan |
| P1-10 | deployment preflight | Dependency/version checks without mutation |
| P1-11 | final state versus logs | Atomic/ordered publication; simulated DB and filesystem failure |
| P1-12 | Task versus environment version | Queue-time drift rejected before execution |

## Evidence register
- 2026-11-03 Batch 2 archive: located; later patch inclusion **unconfirmed**.
- Prior conversation: reported partial security fixes and test counts; **not a substitute for repeatable evidence**.
- Ubuntu real-machine acceptance: **not run / no verified result available**.

## Next engineering batch
1. Obtain the newest actual working tree or consolidated patch, compare against Batch 2, and capture checksums.
2. Implement P0-03 isolation and P0-04/P0-05 process lifecycle with integration tests.
3. Complete P0-02/P0-06 SQLite WAL recovery drill and P1-08 DB threat model.
4. Execute all P1 regression cases and isolated Ubuntu acceptance; attach command output, environment versions and source revision.

No automatic merge or production deployment.
