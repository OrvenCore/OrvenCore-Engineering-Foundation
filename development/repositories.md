# Repository Standards

> **Status:** Approved — 2026-08-15

An OrvenCore repository should let a new contributor understand what the product is, how to work on it safely, and how it is released without private oral history.

## Naming

Public product names follow the [OrvenCore Brand](https://github.com/OrvenCore/brand): `Orven + Product Name`, such as **Orven Sync**.

Technical repositories **SHOULD** use lowercase kebab-case:

```text
orven-sync
orven-vault
orven-journal
```

Foundation repositories such as `brand` and `ocef` MAY use their established names. A repository name **SHOULD NOT** encode a temporary framework, version, or deployment environment unless that is the repository's actual responsibility.

## Baseline contents

Every maintained product repository **MUST** contain or link to:

- `README.md` with purpose, status, supported platforms, setup, configuration, security notes, and support;
- source code in a predictable location;
- automated tests;
- dependency manifests and lockfiles where the ecosystem supports them;
- build, run, and test instructions that work from a clean checkout;
- configuration documentation and safe examples; and
- CI configuration for required checks.

As applicable, repositories **SHOULD** include:

- `LICENSE` with the approved license;
- `SECURITY.md` with supported versions and a private reporting route;
- `CHANGELOG.md`;
- `CONTRIBUTING.md`;
- `docs/` for architecture, threat model, decisions, and operations;
- `examples/` containing safe, maintained examples;
- `scripts/` or task-runner commands for repeatable local workflows; and
- `.editorconfig`.

Use the [project README template](../templates/project-readme.md). Sections that do not apply MAY be omitted with no empty placeholders.

## Suggested layout

Technology conventions take precedence when they are well established, but the result **SHOULD** remain recognizable:

```text
project/
├── README.md
├── LICENSE
├── SECURITY.md
├── CHANGELOG.md
├── src/
├── tests/
├── docs/
├── scripts/
├── examples/
└── .github/workflows/
```

Generated files and vendored code **SHOULD** be visually or structurally separated from authored source.

## README and documentation

Documentation **MUST** match the supported release. Commands **SHOULD** be copyable and include their working directory and prerequisites.

The README **MUST NOT**:

- describe draft or unverified behavior as stable;
- contain real secrets or private infrastructure details;
- make absolute security claims;
- hide critical prerequisites in issue history; or
- present generated concept assets as canonical brand assets.

Architecture, threat models, and runbooks MAY live outside the repository only when the README links to an access-controlled source that all relevant maintainers can reach.

## Git model

OrvenCore uses a simple trunk-based model:

- `main` is the default, releasable branch;
- short-lived feature/fix branches are preferred;
- changes normally enter `main` through pull requests;
- release branches MAY be used only when a maintained release line needs separate support.

Long-lived environment branches such as `staging` and `production` **SHOULD NOT** represent deployments. Environments should receive traceable artifacts from reviewed revisions.

## Protected default branch

Where hosting capabilities allow, `main` **MUST** be protected or governed by an equivalent ruleset that:

- blocks deletion and force pushes;
- requires applicable CI checks;
- requires pull requests for non-emergency changes;
- prevents unresolved review conversations from being ignored; and
- limits bypass authority.

Administrators **SHOULD** be subject to the same protections. A documented emergency bypass MAY exist under [Release Standards](../lifecycle/release.md#emergency-releases).

## Pull requests and review

A pull request **SHOULD**:

- state the problem and intended behavior;
- remain small enough to review;
- identify security, data, platform, configuration, migration, and operational impact;
- link relevant requirements, threats, or decisions;
- include tests and documentation; and
- report known limitations and follow-up work.

Security-sensitive changes **MUST** receive focused review. When a qualified second maintainer exists, high-risk changes **SHOULD** require their approval.

A solo maintainer MAY approve and merge their own work only after:

- CI passes;
- a deliberate second-pass diff review occurs after implementation;
- security/operations impact is recorded; and
- the applicable checklist is completed.

Review **MUST NOT** be reduced to approving a green CI status. Automated checks do not assess intent, architecture, or missing authorization.

## Commits

Commits **SHOULD** be cohesive and use clear imperative messages, for example:

```text
Validate tenant ownership before serving exports
```

Conventional Commits MAY be adopted per project but are not an OcEF requirement.

Commits **MUST NOT** intentionally include:

- secrets or credentials;
- unrelated generated output;
- personal editor or OS state;
- unexplained binary blobs; or
- disabled tests/scans intended only to make CI pass.

History rewriting on a shared protected branch **MUST NOT** occur except for a controlled incident response with maintainer coordination.

## Tags and releases

- Version tags **MUST** identify immutable released content.
- SemVer projects **SHOULD** use `vMAJOR.MINOR.PATCH` tags.
- Stable release tags **SHOULD** be annotated and signed where practical.
- Release artifacts **MUST** be produced and verified according to [Release Standards](../lifecycle/release.md).
- A tag **MUST NOT** be moved to different content after publication.

## Ignore rules

Every repository **MUST** maintain a technology-appropriate `.gitignore` before local configuration is created.

The following **MUST NOT** be committed unless a documented repository-specific reason exists:

```gitignore
.env
.env.*
!.env.example
*.pem
*.key
*.p12
*.pfx
.DS_Store
Thumbs.db
desktop.ini
```

Also ignore:

- virtual environments and package caches;
- IDE user state;
- logs, coverage output, local databases, and temporary files;
- compiled/build output unless the ecosystem requires checked-in generated content; and
- local deployment overrides.

An ignore rule is not a security control. Secret scanning and developer care are still required.

## Dependencies and generated content

- Dependency changes **MUST** include the manifest and lockfile.
- Lockfiles **MUST NOT** be regenerated unnecessarily.
- New direct dependencies **MUST** be justified and reviewed under the [Security Baseline](../security/baseline.md#dependencies-and-software-supply-chain).
- Generated code **MUST** identify its source and reproducible generation command.
- Checked-in generated output **SHOULD** be used only when it materially improves consumer usability, review, or bootstrapping.
- Vendored third-party code **MUST** retain license/provenance information and an update method.

## Ownership, archival, and transfer

Every maintained repository **MUST** have a responsible maintainer and a clear support status.

Before archive or transfer:

- active production/deployment dependencies **MUST** be identified;
- security advisories and supported versions **MUST** be clear;
- credentials and automation **MUST** be revoked or transferred;
- package and release publishing ownership **MUST** be updated;
- the README **MUST** state archival or replacement status; and
- the [deprecation process](../lifecycle/development.md#12-deprecation) **MUST** be complete.
