# Containers and Deployment

> **Status:** Approved — 2026-08-15

OrvenCore's initial production model is a hardened Linux host running Docker and Compose behind a reverse proxy. Containers package applications; they are not a complete security boundary or a substitute for host hardening.

## Container image baseline

Production images **MUST**:

- use a minimal, maintained base appropriate to the runtime;
- pin or deliberately manage the base version; `latest` **MUST NOT** be the production versioning strategy;
- use multi-stage builds where they reduce compilers, package managers, and source from the runtime image;
- contain only runtime dependencies;
- run as a non-root user where technically practical;
- define a specific entry point and predictable shutdown behavior;
- avoid embedded secrets and environment-specific configuration; and
- be rebuildable from version-controlled instructions.

For high-risk services, the production image **SHOULD** be pinned by immutable digest with a tested automated update path. Pinning without an update process creates stale certainty.

Package installation **SHOULD** be non-interactive, version-controlled, and cleaned in the same layer. Build context **MUST** use `.dockerignore` to exclude secrets, local state, VCS metadata where unnecessary, and large unrelated files.

## Runtime hardening

Containers **SHOULD**:

- use a read-only root filesystem;
- write only to explicit temporary or persistent mounts;
- drop Linux capabilities and add back only those required;
- set `no-new-privileges`;
- use a minimal init when child-process reaping or signal forwarding requires it;
- have memory, CPU, PID, and storage limits where exhaustion could affect other services; and
- expose a health signal that tests meaningful local readiness.

Containers **MUST NOT**:

- use `privileged: true` without a founder-approved exception;
- mount the Docker socket into an application container;
- mount broad host paths when a narrow volume or file suffices;
- share the host PID, IPC, or network namespace without documented necessity;
- run SSH for ordinary application administration; or
- rely on container names or network placement as authentication.

Root inside a container still matters. Non-root execution **MUST** be the target; an exception requires a technical reason and compensating restrictions.

## Health checks and lifecycle

- Health checks **MUST** be lightweight, bounded, and free of secret or personal data.
- Liveness **MUST NOT** depend on an external service in a way that creates restart storms.
- Readiness **SHOULD** identify whether the service can perform its intended local role.
- Processes **MUST** handle the platform's termination signal and finish or stop work within the deployment grace period.
- Startup **MUST** tolerate dependencies becoming ready later or use explicit readiness coordination; Compose startup order alone is not proof of readiness.
- Background workers **SHOULD** make in-flight work idempotent or recoverable.

## Persistent data

- Durable data **MUST** live outside the replaceable container layer.
- Volumes and host mounts **MUST** have documented ownership, permissions, backup, retention, migration, and recovery.
- Application containers **SHOULD NOT** receive direct access to unrelated service data.
- Database and uploaded-file persistence **MUST** be included in the product's backup inventory.
- Temporary data **SHOULD** have size and cleanup controls.

Deleting and recreating a container **MUST NOT** delete user data unless that behavior is explicit and safe.

## Secrets and configuration

- Images **MUST** be environment-neutral.
- Real secret values **MUST NOT** appear in Dockerfiles, Compose files, image labels, build arguments, layers, or repository environment files.
- Secrets **SHOULD** be injected through mounted secret files, workload identity, or an approved secret manager.
- Non-secret configuration **SHOULD** be explicit, validated at startup, and documented.
- Production debug flags, development servers, and verbose error modes **MUST** be disabled.

See [Secrets and Cryptography](../security/secrets-cryptography.md).

## Docker Compose

Production Compose configuration **MUST**:

- be version-controlled and reviewable;
- use explicit image versions or digests;
- declare networks, volumes, mounts, health checks, restart behavior, and service dependencies intentionally;
- avoid unnecessary host port publication;
- use the shared reverse-proxy network only for services that require it;
- keep data services on private networks;
- document every required environment/configuration value;
- separate reusable configuration from secret values; and
- pass `docker compose config` or equivalent validation before deployment.

Compose overrides MAY adapt environments, but the effective production configuration **MUST** be reviewable and reproducible. A file edited only on the server is not a deployment source of truth.

Builds **SHOULD** occur in CI or a controlled build environment. Production hosts **SHOULD** pull and run the reviewed artifact rather than compile application source.

## Network exposure and reverse proxy

- Only intended public services **MUST** be reachable from the public internet.
- Public HTTP services **SHOULD** be exposed through the managed reverse proxy with TLS, not by directly publishing application ports.
- Databases, caches, queues, metrics endpoints, container APIs, and administrative dashboards **MUST NOT** be publicly exposed unless the architecture explicitly requires and strongly protects them.
- Host firewall policy **MUST** default to deny unsolicited inbound traffic except documented services.
- Services **MUST** authenticate and authorize requests even on internal networks when the operation is protected.
- Forwarded client identity and protocol headers **MUST** be trusted only from known proxy hops and parsed through framework support.

An internal hostname, private IP, VPN, or Compose network is not a security boundary by itself.

## Environments

Development, staging, and production **MUST** be separated at least logically:

- distinct credentials and tokens;
- distinct data stores or schemas with controlled access;
- environment-specific domains and configuration;
- independent deployment targets and audit history; and
- no automatic path from untrusted development code to production secrets.

Production personal data **MUST NOT** be copied into development or staging unless the need is approved, data is minimized or de-identified, access is controlled, and retention is limited.

Staging **SHOULD** resemble production in architecture and configuration shape without sharing production trust.

## Deployment process

Every deployed service **MUST** have a short runbook defining:

- prerequisites and target environment;
- artifact and configuration source;
- pre-deployment checks and backup/migration needs;
- deployment command or automation;
- health and smoke verification;
- monitoring and log locations;
- rollback or forward-recovery procedure; and
- responsible owner.

Deployments **MUST** be:

- traceable to a source revision, artifact digest, actor, and time;
- repeatable from version-controlled automation;
- protected by scoped deployment credentials;
- observable during and after change; and
- rollback-capable or backed by a documented forward-recovery strategy.

Manual production changes **MUST** be captured in automation or reverted after the incident. “SSH into the host and remember the steps” is not a deployment process.

## Database migrations

- Migrations **MUST** be version-controlled and run through an explicit deployment step.
- The application **MUST NOT** require broad schema-administrator privileges during routine operation.
- Migration locks, duration, compatibility, and failure behavior **MUST** be tested.
- Destructive changes **SHOULD** use expand-migrate-contract sequencing.
- Automatic startup migrations MAY be used for low-risk single-instance products only when concurrency, backup, failure, and rollback behavior are controlled.
- A migration **MUST NOT** be marked successful until post-conditions are verified.

## Image and host security

- Images **MUST** receive vulnerability scanning before stable release and on a maintenance cadence.
- Base-image and OS package updates **MUST** have an owned process.
- The Docker host **MUST** be patched, access-controlled, firewalled, monitored, and backed up according to [Operations](operations.md).
- Docker daemon access **MUST** be treated as host-root-equivalent.
- Remote daemon APIs **MUST NOT** be exposed without strong mutual authentication, authorization, and network restriction.
- Unused images, volumes, and containers **SHOULD** be removed through a controlled process that protects active data.

Nested virtualization or orchestration **SHOULD NOT** be introduced unless it solves a documented requirement that simpler Docker deployment cannot.
