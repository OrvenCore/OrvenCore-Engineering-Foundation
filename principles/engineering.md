# Engineering Principles

> **Status:** Approved — 2026-08-15

These principles define how OrvenCore engineers make decisions when no narrower rule answers the question. Specific requirements elsewhere in OcEF take precedence over general guidance here.

## Security by design

Security is an architectural property, not a release-week feature.

- Meaningful assets, attackers, entry points, permissions, failure modes, and trust boundaries **MUST** be considered before major implementation.
- Security-sensitive decisions **MUST** be visible in the architecture or threat model.
- New features **MUST NOT** silently widen access, data collection, or network exposure.

Why: controls added after an architecture is fixed are usually weaker, more expensive, and easier to bypass. See [Development Lifecycle](../lifecycle/development.md) and [Security Baseline](../security/baseline.md).

## Least privilege

Users, processes, services, containers, databases, CI jobs, tokens, and administrators **MUST** receive only the permissions needed for their current task.

Privileges **SHOULD** be scoped by operation, resource, environment, and time. Broad access granted “for convenience” **MUST NOT** become an undocumented permanent dependency.

## Explicit trust boundaries

Trust is earned by verified identity, authorization, integrity, and context—not by network location or obscurity.

- Systems **MUST** identify where data or control crosses between users, processes, services, networks, environments, and third parties.
- An internal hostname, private address, VPN, container network, or reverse proxy **MUST NOT** be treated as an authorization control by itself.
- Every boundary **SHOULD** have a named validation, authentication, authorization, or isolation mechanism.

## Secure defaults

The default path **MUST** be the safe path.

Dangerous capabilities, public exposure, verbose error detail, permissive cross-origin access, debug modes, and broad permissions **MUST NOT** be enabled by default. If a user deliberately enables risk, the effect **SHOULD** be clear and reversible.

## Fail safely

Failure **MUST NOT** silently bypass authorization, expose secrets, corrupt durable data, or leave partially applied security changes presented as successful.

Operations that change important state **SHOULD** be atomic, idempotent, transactional, or recoverable. Error messages **SHOULD** help the intended user while withholding unnecessary internals from untrusted clients.

## Maintainability over cleverness

Code is read and operated for longer than it is first written.

- Clear, conventional code **SHOULD** be preferred to compact or surprising abstractions.
- Security-critical behavior **MUST NOT** depend on undocumented magic.
- A future maintainer **SHOULD** be able to understand the intent, failure modes, and tests without reconstructing the original author's thought process.

“Boring” is positive when it means proven, observable, and easy to replace.

## Minimal necessary complexity

Every service, abstraction, dependency, queue, database, framework, and deployment layer adds failure modes.

- Architecture **SHOULD** use the fewest moving parts that meet the product's actual requirements.
- New complexity **SHOULD** have a stated benefit greater than its security, operational, and maintenance cost.
- Premature distribution, microservices, or generalization **SHOULD NOT** replace a simpler design.

Simplicity does not mean omitting required controls. It means implementing them directly and deliberately.

## Reproducibility

Another competent engineer **SHOULD** be able to build, test, configure, and deploy the same revision from documented inputs.

- Source revisions, dependency versions, build instructions, and configuration contracts **MUST** be versioned or recorded.
- Production changes **MUST NOT** depend on undocumented manual edits.
- Release artifacts **SHOULD** be traceable to source and CI execution. High-risk products **SHOULD** add provenance or attestation.

## Observability

A system that cannot explain its state cannot be operated reliably.

Production systems **MUST** expose enough health, metrics, logs, and audit context to detect meaningful failures and investigate incidents without logging secrets or unnecessary personal data. Signals **SHOULD** answer what happened, where, when, and for which safe correlation identifier.

See [Operations](../infrastructure/operations.md).

## Cross-platform thinking

For executable products, Windows, macOS, and Linux support **MUST** be evaluated during requirements and architecture.

Where all three are technically feasible, OrvenCore applications **MUST** support all three. OS-specific behavior **MUST** be isolated behind clear interfaces and tested on every claimed platform. “It builds on the primary developer machine” is not platform support.

See [Cross-Platform Engineering](../development/cross-platform.md).

## Dependency restraint

Every dependency is code OrvenCore must trust, update, license, scan, and sometimes replace.

- A dependency **SHOULD** solve a meaningful problem better than a small, maintainable local implementation.
- Direct dependencies **MUST** be inventoried through the ecosystem lockfile or equivalent.
- Unsupported, unmaintained, unexpectedly privileged, or unjustifiably large dependencies **SHOULD NOT** be introduced.
- Removing a dependency is a valid engineering improvement.

## Defense in depth

No single control **MUST** be assumed infallible.

Important assets **SHOULD** be protected by independent layers—for example, server-side authorization plus scoped database access; input validation plus parameterized queries; network restriction plus service authentication; backup plus restore testing.

Layers are useful only when each addresses a realistic failure and can be operated correctly. More controls are not automatically better.

## Privacy and data restraint

OrvenCore **MUST** collect, retain, and expose only data required for a documented purpose.

Data sensitivity, retention, deletion, access, and backup behavior **MUST** be considered before collection begins. Privacy **MUST NOT** rely on users discovering hidden configuration.

## Transparency

Important architecture decisions, limitations, supported environments, security properties, operational assumptions, and known risks **MUST** be described accurately.

OrvenCore **MUST NOT** describe software as:

- unhackable;
- unbreachable;
- perfectly or 100% secure; or
- guaranteed vulnerability-free.

Trust comes from concrete properties, evidence, responsible disclosure, and continuous improvement—not absolute claims.

## Craftsmanship

Quality includes architecture, code, tests, accessibility, interfaces, performance, documentation, deployment, error handling, and operational recovery.

Work is complete when the product can be safely understood, released, operated, and maintained—not merely when the happy path runs.
