# Testing and Continuous Integration

> **Status:** Approved — 2026-08-15

Tests provide evidence about defined behavior and risk. They are not a ritual and code coverage is not the product.

## Test strategy

Every project **MUST** identify:

- critical user and operational flows;
- security and data-integrity boundaries;
- supported platforms and configurations;
- important failure and recovery paths;
- external integrations to simulate or test; and
- which test layer provides the best evidence for each risk.

Tests **SHOULD** be deterministic, isolated, readable, and fast enough to run at the cadence where they provide value.

## Test layers

| Layer | Expected use |
| --- | --- |
| **Unit** | Pure logic, parsing, policy, transformations, failure branches |
| **Integration** | Database, filesystem, queues, services, migrations, container boundaries |
| **API/contract** | Authentication, authorization, schemas, errors, idempotency, compatibility |
| **End-to-end** | A small set of critical journeys across the deployed system |
| **Regression** | Every material defect or vulnerability gets a test when reproducible safely |
| **Security** | Negative authorization, malicious input, abuse limits, secret handling, unsafe defaults |
| **Recovery** | Migration interruption, restore, retry, rollback, partial failure |
| **Platform** | Build, package, install, update, and core flows on claimed operating systems |

Not every project needs every layer. A missing layer **SHOULD** reflect absent risk, not inconvenience.

## What must be tested

Applicable projects **MUST** test:

- critical business and user paths;
- access control for allowed and denied actors;
- object and tenant isolation;
- malformed, boundary, oversized, and unexpected input;
- error and timeout behavior;
- persistence and migration behavior;
- secure defaults and configuration validation;
- retry/idempotency and concurrent updates where relevant;
- platform-specific paths and process behavior;
- upgrade and rollback/recovery paths; and
- regressions for material bugs and vulnerabilities.

Tests **MUST NOT** use real production secrets or unnecessary production personal data.

## Security tests

Security-sensitive code requires stronger evidence than happy-path tests.

- Authentication tests **SHOULD** cover enumeration resistance, session rotation/revocation, recovery, and rate controls.
- Authorization tests **MUST** include anonymous, wrong-user, wrong-tenant, insufficient-role, and stale-permission cases as applicable.
- Input tests **SHOULD** include injection metacharacters, traversal forms, alternate encodings, parser depth, and size exhaustion relevant to the boundary.
- File tests **SHOULD** cover mismatched type, unsafe name, traversal, archive expansion, and unauthorized download.
- Outbound request tests **SHOULD** cover blocked address ranges, redirects, DNS changes where testable, timeouts, and size limits.
- Secret tests **SHOULD** verify redaction and absence from artifacts/logs.

Automated dynamic scanners MAY supplement these tests, but **MUST NOT** replace application-specific authorization and abuse-case tests.

## Coverage

OcEF does not require 100% line coverage.

Coverage **SHOULD** expose untested important code, not reward trivial assertions. Projects MAY set a baseline to prevent unexplained regression, but:

- critical authorization, identity, cryptographic integration, data transformation, and recovery logic **SHOULD** have high decision/branch coverage;
- generated code and unreachable platform shims MAY be excluded with rationale; and
- a high percentage **MUST NOT** be presented as proof of correctness or security.

Mutation or property-based testing MAY be used when it gives stronger evidence for parsers, policy engines, serialization, or complex invariants.

## Flaky and slow tests

- A test that fails intermittently **MUST** be treated as a defect.
- Flaky required tests **MUST NOT** be silently retried until green without retaining the failure signal.
- Quarantine MAY be used briefly when it has an owner, issue, reason, and review date.
- Slow suites **SHOULD** be split by cadence, but release-blocking coverage **MUST** run before release.
- External-service tests **SHOULD** use deterministic contracts or controlled environments and still retain a small real integration check where needed.

## CI baseline

The default branch and pull requests **MUST** run applicable checks in a clean environment:

```text
format/lint → unit/integration tests → build/package
            → dependency/secret/SAST scans
            → platform and artifact validation
```

The exact ordering MAY be parallelized. Fast, high-signal failures **SHOULD** run early.

Required jobs **MUST** block merge or release when they fail. A skipped job **MUST** be distinguishable from a passing job.

## CI workflow security

CI executes repository-controlled code and is a privileged boundary.

- Workflow token permissions **MUST** default to read-only and be elevated per job.
- Untrusted pull-request code **MUST NOT** receive secrets, production network access, signing keys, or privileged runners.
- Pull-request workflows using elevated target-branch context **MUST NOT** execute untrusted checked-out code.
- Third-party actions **SHOULD** be pinned to a full immutable commit identifier and updated through reviewed automation.
- Build and test jobs **SHOULD** be separated from signing and deployment jobs.
- Production deployments **MUST** use protected environments, scoped credentials, and reviewed artifacts where the platform supports them.
- Self-hosted runners **MUST** be treated as persistent infrastructure; untrusted public contributions **SHOULD NOT** run on them.
- Caches and artifacts **MUST NOT** cross trust boundaries without integrity and poisoning considerations.
- Logs and artifacts **MUST** follow [secret-handling rules](../security/secrets-cryptography.md#cicd).

Dependencies installed in CI **MUST** be resolved from lockfiles or other reviewed, integrity-aware inputs where possible.

## Platform matrix

Executable products claiming Windows, macOS, and Linux support **MUST** run meaningful CI on all three. A compile-only job is insufficient when runtime behavior can differ.

The matrix **SHOULD** include:

- the minimum and current supported runtime/toolchain;
- path, permission, process, encoding, and line-ending behavior;
- package or installer creation;
- clean install and first run; and
- upgrade/uninstall when the product owns those paths.

Platform-specific tests MAY be isolated, but common behavior **SHOULD** share the same suite to prevent drift. See [Cross-Platform Engineering](cross-platform.md).

## Migrations and recovery

Database or durable-format migrations **MUST** be tested:

- from every supported upgrade source;
- with representative scale and invalid edge cases;
- for interruption or partial failure;
- for compatibility during rollout; and
- with the documented rollback or forward-recovery path.

Products whose recovery depends on backups **MUST** periodically restore a representative backup. Mocking the restore command is not a restore test.

## Test evidence and release

CI **SHOULD** retain:

- source revision;
- workflow and tool versions;
- test and scan outcomes;
- produced artifact digests;
- platform matrix results; and
- approved suppressions or exceptions.

Release artifacts **SHOULD** be the artifacts tested by CI. Rebuilding after tests creates a different, unverified artifact unless reproducibility is independently verified.
