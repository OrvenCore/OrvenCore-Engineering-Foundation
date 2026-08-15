# Release Checklist

> Copy this checklist into the release issue or repository. Record links to evidence. Mark an item **N/A** only with a reason. A mandatory applicable control cannot be skipped by writing N/A; use the [OcEF exception process](../README.md#exceptions).

## Release record

| Field | Value |
| --- | --- |
| Product | [Name] |
| Version | [Version] |
| Classification | [Internal / Alpha / Beta / Stable] |
| Source revision | [Commit] |
| Release owner | [Name/role] |
| Target environments/platforms | [List] |
| Planned date | [YYYY-MM-DD] |
| Risk summary | [Low / Standard / High plus key change] |

## Engineering

- [ ] Release scope is frozen, understood, and linked.
- [ ] Required format and lint checks pass.
- [ ] Unit, integration, API, end-to-end, and regression tests pass as applicable.
- [ ] A clean build succeeds from documented inputs.
- [ ] Each claimed operating system/architecture has meaningful validation.
- [ ] Clean install/first run succeeds on distribution targets.
- [ ] Configuration names, defaults, validation, and precedence are reviewed.
- [ ] No debug, test, or development-only behavior is enabled in production artifacts.
- [ ] Database or durable-format migrations are tested on representative data.
- [ ] Migration interruption, compatibility, and recovery behavior are understood.
- [ ] Performance/capacity impact is measured where the change could be material.
- [ ] Flaky, skipped, or quarantined tests are reviewed and do not hide release risk.

Evidence: [CI run, test report, platform matrix, migration result]

## Architecture and security

- [ ] Architecture and important decisions match the implementation.
- [ ] Threat model is current for changed data, boundaries, entry points, privileges, and dependencies.
- [ ] Highest-risk abuse cases have negative tests or another verification method.
- [ ] Authentication changes are reviewed.
- [ ] Authorization and object/tenant isolation changes are reviewed and tested.
- [ ] Session, token, OAuth/OIDC, MFA, and recovery changes are reviewed as applicable.
- [ ] Input, query, command, template, URL, path, serialization, and file handling use safe APIs.
- [ ] Secret scan passes; no credential is present in source, history added by this release, logs, images, or artifacts.
- [ ] Dependency and license changes are reviewed.
- [ ] Dependency vulnerability scan is triaged.
- [ ] SAST findings are triaged.
- [ ] Container and infrastructure/configuration scans are triaged as applicable.
- [ ] Known vulnerabilities and suppressions have reachability/impact rationale.
- [ ] No release-blocking finding remains without an approved exception.
- [ ] Production permissions, service identities, networks, ports, and database roles follow least privilege.
- [ ] Security documentation and private reporting route are current.

Evidence: [Threat model, scan runs, review, exception IDs]

## Data and privacy

- [ ] New or changed data collection has a documented purpose.
- [ ] Classification, access, retention, deletion, export, and backup behavior are defined.
- [ ] Logs, analytics, crash reports, and support bundles exclude secrets and unnecessary personal data.
- [ ] Third-party data sharing and failure behavior are documented.
- [ ] Destructive behavior has confirmation, authorization, recovery, and honest user communication.

Evidence: [Data inventory, privacy notes, tests]

## Operations

- [ ] Deployment uses the reviewed artifact and traceable configuration.
- [ ] Pre-deployment backup or snapshot is completed where the change requires it.
- [ ] Rollback or forward-recovery procedure is available and credible.
- [ ] Last known-good artifact/configuration is identified.
- [ ] Health checks and smoke tests are defined.
- [ ] Monitoring, dashboards, and alerts reflect new behavior and have an owner.
- [ ] Background jobs, certificate, backup, capacity, and dependency monitors are updated.
- [ ] Log and audit events are verified without sensitive content.
- [ ] Runbooks, access paths, and incident contacts are current.
- [ ] Staged/canary rollout and stop conditions are defined for high-risk changes.

Evidence: [Runbook, backup result, rollback rehearsal, monitor links]

## Product and documentation

- [ ] Version follows the project's documented policy.
- [ ] Changelog is updated.
- [ ] Release notes explain user-visible changes, breaking changes, migrations, and required action.
- [ ] Installation, upgrade, configuration, usage, support, and uninstallation docs are current.
- [ ] Supported platforms and versions are accurate.
- [ ] Known limitations and unresolved non-blocking defects are documented.
- [ ] Screenshots and examples match the release and contain no sensitive data.
- [ ] Security claims describe concrete properties and make no absolutes.

Evidence: [Documentation and release-note links]

## Artifacts

- [ ] Artifacts come from the recorded source revision and controlled build.
- [ ] Artifact names, versions, metadata, and platform identifiers are correct.
- [ ] Cryptographic checksums are generated and published through the trusted channel.
- [ ] Platform signing/notarization is complete where applicable.
- [ ] SBOM is generated where required or justified by risk.
- [ ] Provenance/attestation is generated where required or justified by risk.
- [ ] Installers, archives, containers, and update metadata are scanned and smoke-tested.
- [ ] Signing/publishing credentials were not exposed to untrusted build steps.

Evidence: [Artifact digests, signing/notarization, SBOM/provenance]

## Final decision

- [ ] All applicable mandatory controls pass or have a documented approved exception.
- [ ] Residual risk and user impact are understood by the release owner.
- [ ] Release, rollback, monitoring, and communication owners are available.
- [ ] Final approval is recorded below.

### Exceptions and N/A rationale

| Item | N/A or exception | Reason / control | Owner | Review / expiry |
| --- | --- | --- | --- | --- |
| [Checklist item] | [N/A / exception ID] | [Justification] | [Owner] | [Date/trigger] |

### Approval

| Role | Name | Decision | Date |
| --- | --- | --- | --- |
| Release owner | [Name] | [Approve / Reject] | [YYYY-MM-DD] |
| Security reviewer, when required | [Name or solo self-review] | [Decision] | [YYYY-MM-DD] |

### Deployment verification

- [ ] Deployed version and artifact digest match the release.
- [ ] Migrations completed and were verified.
- [ ] Health and critical smoke tests pass.
- [ ] Monitoring shows no release-related regression.
- [ ] Rollback remains available until the defined observation window closes.

Outcome and observations: [Short record]
