# Release Standards

> **Status:** Approved — 2026-08-15

A release is a traceable, tested set of artifacts and instructions—not merely a Git tag or deployed commit.

## Release classifications

| Classification | Meaning | Production data |
| --- | --- | --- |
| **Internal** | Limited evaluation by OrvenCore maintainers. Interfaces may change without notice. | **SHOULD NOT** use irreplaceable production data. |
| **Alpha** | Early external testing; incomplete and expected to change. | Only with explicit risk acknowledgement and recovery controls. |
| **Beta** | Feature-complete candidate being validated under realistic use. Known limitations remain. | MAY be used when migration, backup, monitoring, and support are ready. |
| **Stable** | Supported, documented, release-gated, and suitable for its declared production scope. | Yes, within documented guarantees and limits. |

“Stable” does not mean defect-free. It means the product has a defined compatibility contract, operational owner, security baseline, and credible maintenance path.

## Versioning

Projects with a public API, package interface, file format, protocol, CLI contract, or externally consumed behavior **SHOULD** use [Semantic Versioning 2.0.0](https://semver.org/):

- **MAJOR** for incompatible public-contract changes;
- **MINOR** for backward-compatible capability;
- **PATCH** for backward-compatible fixes.

The public contract **MUST** be documented. Pre-`1.0.0` versions MAY change more rapidly but **MUST** still communicate breaking changes.

Products for which SemVer would mislead MAY use a documented alternative such as calendar versioning. A repository **MUST NOT** switch versioning schemes without documenting migration and compatibility consequences.

Released artifacts **MUST NOT** be replaced in place. A corrected artifact receives a new version.

## Changelog and release notes

Every user-facing stable release **MUST** have:

- a version and release date;
- a concise description of user-visible changes;
- security-relevant fixes described responsibly;
- breaking changes and required actions;
- migrations or configuration changes;
- known limitations; and
- links to upgrade and rollback guidance where applicable.

A `CHANGELOG.md` **SHOULD** be maintained for versioned products. Commit history is not a substitute for release notes.

Security fixes **MUST NOT** disclose exploit detail before affected deployments and users have a reasonable remediation path.

## Build and artifact integrity

Release artifacts **MUST** be produced from a specific source revision by documented, repeatable instructions.

- Dependencies and toolchains **MUST** be pinned or otherwise resolved deterministically.
- CI-produced artifacts **SHOULD** be promoted between environments rather than rebuilt for each environment.
- Each downloadable binary, archive, installer, or image **MUST** have a cryptographic digest available through a trusted release channel.
- Public executable artifacts **SHOULD** be platform-signed where practical.
- Signing keys **MUST** follow [Secrets and Cryptography](../security/secrets-cryptography.md).
- High-risk or widely distributed releases **SHOULD** include machine-verifiable provenance or an artifact attestation.
- An SBOM in SPDX or CycloneDX format **SHOULD** accompany high-risk, externally distributed, or customer-managed software.

“Reproducible” **MUST** be used only when the project has verified the same declared inputs can reproduce bit-for-bit identical declared outputs. Other projects may accurately say “repeatable build.”

## Database and state changes

Before release, applicable migrations **MUST** be tested on representative data and backups.

The release plan **MUST** state:

- ordering between application and schema changes;
- expected duration and locking behavior;
- compatibility during rolling or interrupted deployment;
- recovery when a migration partially fails; and
- whether rollback is safe or a forward fix is required.

Destructive migrations **SHOULD** be separated from code rollout using an expand-migrate-contract approach. A backup alone is not a rollback plan unless restore time and data loss meet the product's objectives.

## Configuration and compatibility

New, removed, or changed configuration **MUST** be documented with:

- name, purpose, type, default, and sensitivity;
- validation and failure behavior;
- environment-specific expectations; and
- upgrade or deprecation path.

Secure defaults **MUST** be preserved. Release automation **MUST NOT** silently reset permissions, secrets, retention, or network exposure.

Supported platforms and upgrade paths **MUST** be validated for the release. Dropping a platform or compatibility promise is a breaking product decision even when the code API is unchanged.

## Rollout and rollback

Every production release **MUST** have a rollback or recovery strategy proportional to its risk.

The strategy **MUST** identify:

- the last known-good artifact and configuration;
- triggers and authority for rollback;
- state or migration constraints;
- expected data loss, if any;
- verification after rollback; and
- communication responsibilities.

High-risk changes **SHOULD** use a staged, canary, or limited rollout with observable stop conditions. Rollback **MUST NOT** be assumed possible until the relevant path has been tested or rehearsed.

## Mandatory release gate

All applicable **Required** rows **MUST** pass before a stable production release. “N/A” requires a reason. See the copy-ready [release checklist](../templates/release-checklist.md).

| Gate | Requirement | Evidence |
| --- | --- | --- |
| Scope | **Required** | Approved change set, version, target environments, and owner |
| Quality | **Required** | Required CI, tests, lint/format, builds, and regression checks pass |
| Platforms | **Required when claimed** | Clean build/install and core flows pass on each supported OS |
| Architecture | **Required for material change** | Decisions and diagrams updated |
| Threat model | **Required for meaningful boundaries** | Model reviewed against implementation |
| Security scans | **Required when tools apply** | Dependency, secret, SAST, and image findings triaged |
| Identity and access | **Required when changed** | Focused authentication/authorization review and negative tests |
| Data and migrations | **Required when changed** | Migration, backup, recovery, and retention effects verified |
| Configuration | **Required** | Defaults, secret references, permissions, and exposure reviewed |
| Operations | **Required for deployed services** | Health, monitoring, logging, alerting, capacity, and runbook ready |
| Recovery | **Required** | Tested or credibly rehearsed rollback/recovery plan |
| Documentation | **Required** | Changelog, release notes, setup, upgrade, limitations, and support current |
| Artifacts | **Required** | Traceable artifacts, checksums, and applicable signing/provenance |
| Findings | **Required** | No unresolved release-blocking defect or vulnerability without approved exception |
| Approval | **Required** | Release owner records final decision |

For a solo project, “approval” means a deliberate review separated from implementation, with the checklist and evidence recorded. It does not justify skipping checks.

## Production-ready definition

Software may be called production-ready only when:

- the intended production scope and limitations are documented;
- the system satisfies applicable OcEF requirements or approved exceptions;
- critical paths, failure paths, and supported platforms are verified;
- security boundaries are understood and reviewed;
- deployment and configuration are repeatable;
- monitoring, backup, recovery, and incident ownership exist;
- users have installation, operation, and support guidance; and
- the maintainer is prepared to patch, communicate, and deprecate it.

## Emergency releases

An urgent security or availability fix MAY use an abbreviated process when delay creates greater risk. It **MUST** still:

- identify the incident or risk;
- constrain the change;
- run the fastest relevant tests and security checks;
- preserve a rollback path;
- receive explicit release-owner approval; and
- complete missing evidence, documentation, and follow-up review after stabilization.

Emergency status **MUST NOT** become a routine release method.
