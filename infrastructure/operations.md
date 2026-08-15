# Operations

> **Status:** Approved — 2026-08-15

Production readiness includes the ability to detect failure, protect and restore data, control change, respond to incidents, and retire the service safely.

## Service ownership and runbook

Every production service **MUST** have:

- a named owner and backup contact where available;
- purpose, users, dependencies, and data inventory;
- deployment and rollback/recovery procedure;
- health, monitoring, logs, and alert ownership;
- backup and restore procedure;
- production-access method;
- expected maintenance and patch path; and
- incident and deprecation route.

Runbooks **SHOULD** be executable by a competent maintainer who did not create the service.

## Objectives and capacity

Products **SHOULD** define measurable service objectives proportional to user impact, such as availability, job completion, latency, recovery time, and acceptable data loss.

The following **MUST** be explicit for stateful or important services:

- **RTO**: target time to restore acceptable service;
- **RPO**: maximum acceptable data loss measured in time; and
- capacity or resource thresholds that require action.

Objectives **MUST** reflect actual architecture, backups, and staffing. Unsupported “five nines” style claims **MUST NOT** be made.

## Backups and restore

A backup that has never been successfully restored is not considered fully validated.

The backup plan **MUST** identify:

- databases, uploaded files, application state, secrets/keys where recoverable, configuration, and infrastructure definitions;
- frequency derived from the RPO;
- retention and deletion;
- encryption and access controls;
- at least one off-host or independent failure-domain copy;
- monitoring for failed or stale backups;
- restore order and dependencies; and
- owner and test cadence.

At least one backup copy **SHOULD** be protected from routine application credentials and destructive compromise, for example through immutability, append-only policy, or separate administrative control.

Restore tests **MUST**:

- occur on a schedule appropriate to risk and after material backup changes;
- use a controlled environment;
- verify application-level integrity, not only archive extraction;
- measure achieved RPO and RTO; and
- record date, result, gaps, and follow-up.

Backup encryption keys **MUST** be recoverable through a protected, tested path. A backup whose only key was lost with the host is not a usable backup.

## Monitoring

Deployed services **MUST** monitor applicable:

- external availability and TLS/certificate expiry;
- application readiness and core transaction success;
- error rate, latency, saturation, and resource exhaustion;
- disk, memory, CPU, file descriptors, connection pools, and queue depth;
- database health, replication, and storage growth;
- background/cron job freshness and failure;
- backup completion and age;
- authentication abuse and security-relevant failures; and
- deployment version/configuration drift.

Alerts **MUST** be actionable, routed to an owner, and tested. A dashboard nobody checks is not an alerting system.

Health endpoints **MUST NOT** expose secrets, sensitive data, internal stack traces, or administrative controls.

## Logging

Production applications **SHOULD** use structured logging with:

- UTC timestamp;
- severity;
- service and version;
- environment;
- safe request/trace/correlation ID;
- event name;
- actor or service identity where appropriate;
- outcome and duration; and
- exception class or safe diagnostic context.

Logs **MUST NOT** contain:

- passwords or password hashes;
- raw authentication, access, refresh, reset, or session tokens;
- API secrets, private keys, or full connection strings;
- full payment or government identifiers; or
- unnecessary sensitive personal content.

Log access **MUST** follow least privilege. Retention **MUST** balance investigation, privacy, cost, and legal needs. User-controlled fields **SHOULD** be structured or encoded to prevent log injection and misleading entries.

## Audit logging

High-impact administrative and security actions **SHOULD** create an audit event containing who, what, target, when, source, and outcome.

Applicable events include:

- role and permission changes;
- account disablement, recovery, and MFA changes;
- secret issue, rotation, and revocation;
- configuration and security-policy changes;
- production deployments and rollback;
- sensitive exports and deletions;
- administrative access; and
- audit-log access or retention changes.

Audit logs **SHOULD** be tamper-resistant and separated from ordinary users' modification rights. They **MUST NOT** store the secret or sensitive payload merely to prove the action occurred.

## Production access

- Routine human access **MUST** use individual accounts and strong MFA.
- Shared accounts and shared private SSH keys **MUST NOT** be used for routine administration.
- Access **MUST** be least-privileged, reviewable, and revoked when no longer needed.
- Direct root login **SHOULD** be disabled; controlled elevation **SHOULD** create an audit trail.
- Administrative services **MUST** be network-restricted where practical.
- Production credentials **MUST NOT** be copied into chat, tickets, shell history, or personal notes.
- Access reviews **SHOULD** occur periodically and after personnel/role changes.

Emergency break-glass access MAY exist. It **MUST** be protected separately, monitored, tested, and reviewed after each use.

## Changes and deployment

- Production changes **MUST** be traceable to a reviewed source/configuration revision or an incident record.
- The effective artifact, digest, configuration version, actor, and time **SHOULD** be recorded.
- Deployments **MUST** verify health and core flows after change.
- Manual emergency changes **MUST** be reconciled into version-controlled automation or removed.
- Risky changes **SHOULD** have maintenance communication and explicit stop/rollback conditions.
- Configuration drift **SHOULD** be detectable.

See [Containers and Deployment](containers-deployment.md) and [Release Standards](../lifecycle/release.md).

## Updates and vulnerability maintenance

Owners **MUST** maintain:

- host operating system and kernel;
- container runtime and base images;
- application runtime and dependencies;
- reverse proxy and TLS tooling;
- databases, queues, and other stateful services; and
- CI actions and build toolchains.

Automated alerts **SHOULD** open trackable update work. Security updates **MUST** be prioritized by exploitability, exposure, privilege, data impact, available mitigation, and operational risk—not score alone.

Unsupported components **MUST** be upgraded, isolated under an approved time-bound exception, or removed.

## Incident response

OrvenCore uses a lightweight cycle:

```text
Detect → Contain → Investigate → Remediate → Recover → Review
```

### Detect

Confirm the signal, record time and reporter, assign an incident owner, estimate severity, and preserve volatile evidence where practical.

### Contain

Limit further harm. Actions MAY include disabling a feature, revoking credentials, restricting network access, isolating a host, pausing deployments, or switching to a safe degraded mode.

Containment **MUST** consider evidence preservation and user impact, but protecting users and data takes priority.

### Investigate

Determine initial access, affected identities, versions, systems, data, time range, persistence, and ongoing attacker capability. Assumptions **MUST** be distinguished from verified facts.

### Remediate

Remove the cause and persistence, patch the vulnerable path, rotate exposed credentials, close unauthorized access, and add tests or controls that address the root cause.

### Recover

Restore from known-good artifacts/data, validate integrity and permissions, monitor for recurrence, and communicate applicable user actions and limitations.

### Review

After stabilization, record timeline, impact, root and contributing causes, what worked, what failed, and owned preventive actions. A blameless review is compatible with clear accountability.

Serious security incidents **MUST** involve the founder or delegated incident authority. External communication **MUST** be accurate, timely for user protection, and free of unsupported absolutes.

## Evidence and privacy

Incident evidence **MUST** be access-controlled, integrity-protected where practical, and retained only as long as needed. Investigation **MUST NOT** become justification for collecting unrelated personal data.

Compromised systems **SHOULD** be rebuilt from known-good sources rather than trusted after ad hoc cleanup when attacker persistence is plausible.

## Continuity and dependency failure

Products **SHOULD** define behavior for loss of important third-party services, DNS, certificates, identity providers, networks, package registries, and maintainers.

Critical dependencies **SHOULD** have:

- bounded timeouts and retries;
- a safe degraded or fail-closed mode;
- cached/offline data only where integrity and staleness are controlled;
- replacement or exit considerations; and
- documented operational contacts and limits.

## Deprecation and shutdown

Before shutting down a production service, the owner **MUST**:

- notify affected users appropriately;
- provide export/migration when promised or reasonably necessary;
- stop new writes and verify final backups as needed;
- revoke credentials, tokens, webhooks, and automation;
- remove DNS, certificates, ports, jobs, monitors, and infrastructure;
- apply documented data retention/deletion;
- archive repositories and operational records; and
- verify no paid or privileged dependency remains unintentionally active.

Turning off a container alone is not deprecation.
