# Development Lifecycle

> **Status:** Approved — 2026-08-15

Every OrvenCore product follows this lifecycle. The artifacts may be a few paragraphs for a low-risk tool or a set of reviewed documents for a high-risk service. The decisions themselves **MUST NOT** be skipped because a project is small.

## Lifecycle at a glance

| Stage | Required outcome before proceeding |
| --- | --- |
| 1. Product proposal | A real problem, intended users, owner, scope, and success signal are identified. |
| 2. Requirements | Functional and quality requirements, data, dependencies, platforms, operations, and explicit non-goals are recorded. |
| 3. Architecture | Components, data flows, storage, external systems, trust boundaries, deployment model, and major trade-offs are understood. |
| 4. Threat modeling | Realistic threats and abuse cases have owners, controls, and accepted residual risk. |
| 5. Repository creation | The repository, baseline documentation, CI plan, configuration contract, and ownership are ready. |
| 6. Implementation | Small reviewed changes follow the architecture and keep tests and docs current. |
| 7. Verification | Applicable automated, manual, security, migration, platform, and recovery tests pass. |
| 8. Security review | Sensitive changes and remaining findings are reviewed against the threat model and baseline. |
| 9. Release preparation | The [release gate](release.md#mandatory-release-gate) is satisfied and rollback is credible. |
| 10. Production | Deployment is controlled, observable, and verified. |
| 11. Maintenance | Ownership, updates, incidents, compatibility, and technical debt are actively managed. |
| 12. Deprecation | Users, data, dependencies, and infrastructure receive a documented end-of-life path. |

## 1. Product proposal

Before implementation, the proposal **MUST** answer:

- What problem exists, and for whom?
- Why should OrvenCore solve it?
- Who owns the product and its production operation?
- What is intentionally out of scope?
- What outcome would demonstrate value?
- Is existing software or a smaller solution sufficient?

A proposal **SHOULD** be rejected or narrowed when its long-term operational cost exceeds its likely value.

## 2. Requirements

Requirements **MUST** cover applicable:

- primary users and privileged roles;
- user journeys and failure behavior;
- data created, received, stored, transmitted, exported, and deleted;
- privacy, retention, and recovery expectations;
- external services, packages, APIs, and platform constraints;
- Windows, macOS, and Linux support decisions;
- authentication, authorization, tenancy, and administrative access;
- availability, performance, capacity, monitoring, backup, and support expectations;
- distribution, update, migration, and deprecation expectations; and
- explicit non-goals.

Requirements **SHOULD** be testable. “Secure,” “fast,” and “reliable” alone are not testable requirements.

The project **MUST** assign a [risk profile](../README.md#applicability-and-proportionality). A high-risk classification increases review and evidence; it does not prescribe a technology.

## 3. Architecture

Architecture work **MUST** occur before major implementation. It **MUST** identify:

- components and their responsibilities;
- important data flows and state ownership;
- entry points and trust boundaries;
- authentication and authorization enforcement points;
- persistent stores and migration strategy;
- external dependencies and failure behavior;
- deployment environments and network exposure;
- platform-specific code and packaging boundaries;
- observability and operational ownership; and
- backup, restore, rollback, and deletion paths.

A diagram is useful when it communicates boundaries or data flow more clearly than prose, but a diagram is not required for its own sake.

Material decisions with non-obvious trade-offs **SHOULD** be recorded as short architecture decision records or equivalent notes containing context, decision, consequences, alternatives, and date. Decisions that are obvious from code **SHOULD NOT** generate paperwork.

## 4. Threat modeling

A threat model **MUST** be completed before major implementation for products with meaningful security boundaries, including user accounts, network exposure, sensitive data, privileged operations, untrusted content, or multi-tenancy.

Use [the OcEF threat-model template](../templates/threat-model.md). At minimum:

1. identify assets and sensitive data;
2. identify actors, attackers, entry points, and trust boundaries;
3. describe abuse cases and failure modes;
4. select mitigations and verification;
5. record residual risk and an owner.

The model **MUST** be revisited when architecture, data use, trust boundaries, identity, public exposure, or privileged behavior materially changes. A checklist copied without project-specific analysis is not a threat model.

## 5. Repository creation

Before feature implementation accelerates, the repository **MUST** have:

- a useful README and explicit status;
- a predictable source and test layout;
- configuration and local-development instructions;
- an applicable license decision;
- a CI plan with at least build and test checks;
- secret-safe ignores and scanning;
- a defined default branch and ownership; and
- a security reporting route when the project will be public or deployed.

See [Repository Standards](../development/repositories.md).

## 6. Implementation

Implementation **MUST**:

- follow the approved architecture or update the decision record;
- validate assumptions at boundaries;
- enforce authorization server-side or at the trusted enforcement point;
- include tests for new behavior and regressions;
- avoid unrelated changes that obscure review;
- update user, developer, configuration, and operational documentation with the code; and
- leave security-relevant behavior explicit.

Prototype code MAY explore uncertainty, but it **MUST NOT** silently become production code. Before promotion, it must pass the same review, testing, and operational standards as other implementation.

Feature flags **MAY** reduce rollout risk. Security controls **MUST NOT** be disabled by a feature flag exposed to untrusted users.

## 7. Verification

Verification depth is risk-based. Applicable checks include:

- unit and property tests;
- integration, API, and end-to-end tests;
- authorization and tenant-isolation tests;
- malformed and adversarial input tests;
- dependency, secret, static, and container scanning;
- database migration and rollback or recovery tests;
- backup restore tests;
- packaging and clean-install tests; and
- supported-platform validation.

Known failures **MUST** be triaged. A failing required check **MUST NOT** be converted to non-blocking merely to release; fix it or record an approved exception.

See [Testing and CI](../development/testing-ci.md).

## 8. Security review

Before the first production release and after material security changes:

- the threat model **MUST** reflect the implemented system;
- authentication, authorization, secret, cryptographic, data-handling, and network changes **MUST** receive focused review;
- high-severity scanner findings **MUST** be resolved, disproven with evidence, or explicitly accepted;
- default configuration and production permissions **MUST** be reviewed; and
- the release owner **MUST** understand residual risk.

High-risk products **SHOULD** receive review by a person other than the primary implementer when a qualified reviewer is available. A solo maintainer **MUST** perform a separate, deliberate self-review and record the evidence used.

## 9. Release preparation and 10. Production

The project **MUST** satisfy [Release Standards](release.md) and the [release checklist](../templates/release-checklist.md).

Deployment **MUST** use the reviewed artifact and configuration, not an untracked local build. After deployment, health, core flows, migration state, version, monitoring, and rollback readiness **MUST** be verified.

## 11. Maintenance

Every maintained product **MUST** have a known owner. Maintenance includes:

- dependency, platform, certificate, and operating-system updates;
- vulnerability triage;
- production monitoring and incident response;
- backup and restore validation;
- compatibility testing;
- documentation corrections;
- user-impacting change communication; and
- periodic review of unused features, data, permissions, and dependencies.

An unowned production system is a risk. It **MUST** be assigned, transferred, or deprecated.

## 12. Deprecation

Deprecation **MUST** be planned rather than achieved by abandonment. The plan **MUST** address:

- reason and owner;
- affected users, clients, integrations, and data;
- announced dates and support window appropriate to impact;
- migration, export, or replacement path;
- credential, DNS, certificate, job, and infrastructure shutdown;
- data retention and verified deletion;
- repository archival and final security notice; and
- rollback of the decommission itself where necessary.

Known critical vulnerabilities in a deprecated but still reachable system **MUST** be mitigated, isolated, or the system removed promptly.

## Keeping process proportional

One document MAY satisfy several lifecycle artifacts when it remains clear. A low-risk CLI might keep requirements, architecture, and a small threat assessment in its README. A public identity service should not.

Documentation depth **SHOULD** scale with:

- sensitivity and volume of data;
- privilege and blast radius;
- public exposure and attacker incentive;
- number of users and integrations;
- operational and migration complexity; and
- irreversibility of failure.

The purpose is earlier thinking and durable context—not document production.
