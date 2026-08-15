<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/brand/orvencore-horizontal-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="./assets/brand/orvencore-horizontal-light.svg">
  <img alt="OrvenCore" src="./assets/brand/orvencore-horizontal-light.svg" width="348">
</picture>

# OrvenCore Engineering Foundation

> **Status: Approved — 2026-08-15**

The **OrvenCore Engineering Foundation (OcEF)** is the authoritative engineering standard for OrvenCore software. It defines how products are proposed, architected, secured, implemented, tested, reviewed, released, operated, maintained, and deprecated.

OcEF exists to make good engineering the default. It is deliberately compact: requirements live close to the decisions they govern, and each rule is intended to be usable by a small team.

## Who should read OcEF

OcEF is written for:

- developers and technical contributors;
- application-security and infrastructure engineers;
- maintainers and reviewers;
- technical project leads; and
- anyone onboarding to an OrvenCore product.

New engineers SHOULD begin with this README, [Engineering Principles](principles/engineering.md), [Development Lifecycle](lifecycle/development.md), and the security or platform documents relevant to their work.

## Brand and engineering

| Source of truth | Governs |
| --- | --- |
| [OrvenCore Brand](https://github.com/OrvenCore/brand) | How OrvenCore looks, speaks, names products, and presents itself |
| **OcEF** | How OrvenCore software is planned, engineered, secured, delivered, and operated |

Product design systems MAY extend the Brand. Product engineering guidance MAY extend OcEF. Extensions MUST NOT contradict their respective source of truth without an approved exception.

This repository's presentation uses the approved **Open Joint** logo system: Orven Blue 600 (`#4657D5`) with Core Ink (`#111820`) on light backgrounds, and Orven Blue 400 (`#7C88FF`) with white on dark backgrounds. The outlined wordmark is used as supplied; it is not recreated with live text. See the [brand asset notice](assets/brand/NOTICE.md). Brand styling never changes the meaning or priority of an OcEF requirement.

## The engineering path

Every product follows the same direction, with depth proportional to risk:

```text
Proposal → Requirements → Architecture → Threat model → Repository
         → Implementation → Automated tests → Security review
         → Release gate → Production → Maintenance → Deprecation
```

Starting implementation before the problem, data, trust boundaries, and release path are understood creates avoidable rework and security debt. Small projects may keep these decisions in a concise README or issue; high-risk products need explicit artifacts.

## Repository map

| Area | Purpose |
| --- | --- |
| [principles/engineering.md](principles/engineering.md) | The engineering culture and non-negotiable foundations |
| [lifecycle/development.md](lifecycle/development.md) | Product stages, required decisions, and proportional documentation |
| [lifecycle/release.md](lifecycle/release.md) | Versioning, production readiness, artifacts, and the release gate |
| [security/baseline.md](security/baseline.md) | Core application-security requirements |
| [security/identity-access.md](security/identity-access.md) | Authentication, authorization, sessions, tokens, and service identities |
| [security/secrets-cryptography.md](security/secrets-cryptography.md) | Secret lifecycle, encryption, hashing, randomness, and key management |
| [security/data-input-files.md](security/data-input-files.md) | Data, databases, input, serialization, URLs, paths, and file handling |
| [development/repositories.md](development/repositories.md) | Repository contents, naming, Git, review, and dependency hygiene |
| [development/testing-ci.md](development/testing-ci.md) | Risk-based testing and secure continuous integration |
| [development/cross-platform.md](development/cross-platform.md) | Windows, macOS, and Linux portability |
| [infrastructure/containers-deployment.md](infrastructure/containers-deployment.md) | Container, Compose, network, environment, and deployment standards |
| [infrastructure/operations.md](infrastructure/operations.md) | Backups, monitoring, logging, access, updates, and incident response |
| [templates/](templates/) | Copy-ready threat model, project README, and release checklist |
| [REFERENCES.md](REFERENCES.md) | Authoritative standards that materially informed OcEF |

## Requirement terminology

The keywords below are interpreted according to [BCP 14](https://www.rfc-editor.org/info/bcp14/) when, and only when, they appear in uppercase:

| Keyword | Meaning |
| --- | --- |
| **MUST** | An absolute OcEF requirement. |
| **MUST NOT** | An absolute prohibition. |
| **SHOULD** | The expected approach. A different approach needs an understood, documented reason. |
| **SHOULD NOT** | Normally prohibited; exceptions need an understood, documented reason. |
| **MAY** | Optional. |

Lowercase uses of “must,” “should,” and “may” have their ordinary English meaning.

## Applicability and proportionality

OcEF applies to every maintained OrvenCore software repository. A control qualified with “where applicable” is applicable when its referenced technology or risk exists; it is not an invitation to ignore the control.

Projects SHOULD record a simple risk profile during requirements:

| Profile | Typical characteristics | Expected depth |
| --- | --- | --- |
| **Low** | Local tool, no privileged access, no sensitive data, small blast radius | Concise decisions and focused tests |
| **Standard** | Network service, user accounts, persistent data, or public distribution | Documented architecture, threat model, CI and release gate |
| **High** | Sensitive data, privileged operations, multi-tenancy, payments, security product, or large operational impact | Detailed threat model, independent review where possible, stronger testing and staged release |

Risk may change during development. When a product crosses a boundary, its controls and documentation MUST be reassessed.

## Exceptions

Silently ignoring OcEF is not an exception.

A deviation from a **MUST** or **MUST NOT** requires a short, version-controlled exception record in the affected repository or its tracked project system. It MUST state:

- the exact rule and document;
- the reason and technical justification;
- the risk introduced;
- compensating controls or mitigation;
- the responsible owner;
- approval by the project maintainer or founder; and
- an expiration or review date when the deviation is temporary.

Emergency action MAY precede the written record when delay would cause greater harm. The record and review MUST follow as soon as the system is stable.

## Ownership and change

This initial OcEF edition was approved by the OrvenCore founder on **2026-08-15** and is authoritative for maintained OrvenCore software.

For future changes:

- material changes SHOULD be reviewed like product code;
- security guidance SHOULD be checked against current primary sources at least annually and after material standards changes;
- contradictions MUST be resolved in favor of the more specific requirement until OcEF itself is clarified; and
- products MAY adopt a newer, safer practice before OcEF is updated when the decision is documented.

OcEF defines a baseline, not a guarantee. Compliance reduces avoidable risk; it does not make software invulnerable.
