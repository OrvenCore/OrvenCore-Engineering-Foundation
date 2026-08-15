# Threat Model Template

> Copy this file into the project repository. Replace bracketed prompts, delete instructions that do not apply, and keep the model with the architecture. A short accurate model is better than a long generic one.

## Document control

| Field | Value |
| --- | --- |
| Project | [Canonical product name] |
| Version / revision | [Version, commit, or architecture revision] |
| Owner | [Responsible person or role] |
| Reviewers | [Names/roles, or “solo self-review”] |
| Created | [YYYY-MM-DD] |
| Last reviewed | [YYYY-MM-DD] |
| Risk profile | [Low / Standard / High] |
| Status | [Draft / Reviewed / Superseded] |

## Scope

### Product and security objective

[In 2–4 sentences: what the system does, for whom, and which security outcomes matter most.]

### In scope

- [Component, environment, flow, or feature]

### Out of scope

- [Explicit exclusion and why]

Out-of-scope systems that exchange data with the project still appear as external dependencies or trust boundaries.

## Architecture overview

[Link to architecture document or add a concise component/data-flow diagram.]

### Components and responsibilities

| Component | Responsibility | Runs where | Privilege / identity |
| --- | --- | --- | --- |
| [Name] | [What it owns] | [Client/server/container/platform] | [User/service/role] |

### Important data flows

| ID | Source → destination | Data | Protocol / protection | Trust boundary crossed |
| --- | --- | --- | --- | --- |
| F-01 | [A → B] | [Data] | [TLS, local IPC, file, etc.] | [Boundary] |

## Assets and data

| Asset / data | Why it matters | Classification | Location(s) | Integrity / availability need |
| --- | --- | --- | --- | --- |
| [Example: user files] | [Impact if read/changed/lost] | [Public/Internal/Confidential/Restricted] | [Store/backup/cache] | [Need] |

Include credentials, signing keys, authorization policy, audit history, source/release artifacts, and availability when they matter.

## Actors

### Intended users and services

| Actor | Legitimate capabilities | Authentication | Important constraints |
| --- | --- | --- | --- |
| [User/service] | [Actions] | [Method] | [Tenant/scope/device] |

### Potential attackers

| Attacker | Capability and access | Motivation / target |
| --- | --- | --- |
| [Anonymous internet user] | [Requests, uploads, cost] | [Abuse/data/service] |
| [Authenticated user] | [Valid account, own tenant] | [Other users/privilege] |
| [Compromised dependency/service] | [Trusted position] | [Supply chain/data] |
| [Malicious or mistaken administrator] | [Privileged access] | [Impact] |

Do not assume every attacker is advanced. Include ordinary mistakes and automation abuse.

## Trust boundaries

| ID | Boundary | Trusted side | Untrusted / less-trusted side | Enforcement |
| --- | --- | --- | --- | --- |
| TB-01 | [Browser ↔ API] | [API policy] | [Client input] | [TLS, validation, authn/authz, limits] |

Examples: user/device ↔ application, internet ↔ reverse proxy, service ↔ database, tenant ↔ tenant, CI ↔ production, host ↔ container, application ↔ third-party API.

## Entry points

| Entry point | Exposed to | Accepted input / action | Authentication | Limits / validation |
| --- | --- | --- | --- | --- |
| [Endpoint, CLI, file, queue, webhook, IPC] | [Actor/network] | [Input] | [Method/none] | [Schema, size, rate] |

## Identity and authorization

### Authentication

[Methods, session/token model, MFA/passkey expectations, recovery, service identities, revocation.]

### Authorization

[Roles/permissions, object and tenant checks, policy enforcement points, admin path, deny behavior.]

### Security invariants

- [Example: A user can read only objects belonging to their current tenant.]
- [Example: Routine application credentials cannot alter database schema.]
- [Example: A disabled account cannot use an existing session.]

Each important invariant should map to a test or operational control.

## External dependencies

| Dependency | Data / privilege shared | Trust assumption | Failure or compromise impact | Mitigation / exit |
| --- | --- | --- | --- | --- |
| [Service/package/platform] | [Access] | [Assumption] | [Impact] | [Control] |

Include identity, email, payment, storage, update, package, DNS, certificate, and build providers where applicable.

## Abuse cases

Write concrete attacker goals before listing generic vulnerability names.

| ID | Abuse case | Actor | Desired impact | Existing prevention / detection |
| --- | --- | --- | --- | --- |
| A-01 | [User requests another tenant's export] | [Authenticated user] | [Data disclosure] | [Object authz + negative test + audit] |

Consider:

- unauthorized access or privilege escalation;
- account takeover and recovery abuse;
- data corruption, deletion, or ransomware;
- resource exhaustion and billable abuse;
- malicious files, URLs, imports, or dependencies;
- secret or token disclosure;
- replay, race, or duplicate processing;
- unsafe update or release substitution;
- administrator misuse and support impersonation;
- monitoring, backup, or recovery failure.

## Threat register

| ID | Threat / failure | Affected asset | Likelihood | Impact | Mitigation | Verification | Owner | Residual risk |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| T-01 | [Specific scenario] | [Asset] | [Low/Med/High] | [Low/Med/High] | [Prevent/detect/recover] | [Test/review/alert] | [Owner] | [Accepted remainder] |

Likelihood and impact are decision aids, not precision measurements. Explain unusual ratings.

## Security requirements derived

- [ ] [Requirement mapped to T-xx or invariant]
- [ ] [Test, alert, runbook, or architecture change]

Items that block release belong in the project tracker and [release checklist](release-checklist.md), not only in this document.

## Residual risk and decisions

### Accepted residual risks

| Risk | Reason acceptance is justified | Compensating controls | Approver | Review / expiry |
| --- | --- | --- | --- | --- |
| [Risk] | [Reason] | [Control] | [Owner/founder] | [Date/trigger] |

### Rejected alternatives

| Alternative | Why it was not selected | Conditions that would change the decision |
| --- | --- | --- |
| [Option] | [Trade-off] | [Trigger] |

## Review triggers

Review this model when any of the following changes materially:

- architecture, trust boundary, or public exposure;
- authentication, authorization, privilege, or tenancy;
- collected data, retention, export, or third party;
- file, URL, plugin, script, or code-execution capability;
- deployment, CI, signing, or update path;
- serious vulnerability or incident; or
- supported platform or operating model.

## Review history

| Date | Version / change | Reviewer | Outcome |
| --- | --- | --- | --- |
| [YYYY-MM-DD] | [Scope] | [Name/role] | [Accepted / actions] |
