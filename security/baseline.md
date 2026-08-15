# Security Baseline

> **Status:** Approved — 2026-08-15

This is the minimum secure-development baseline for OrvenCore software. It translates current authoritative guidance into practical controls; it is not a claim of compliance with every source in [REFERENCES.md](../REFERENCES.md).

Security controls **MUST** be selected for the actual threat, operated correctly, and tested. Adding controls without understanding their cost or failure modes is security theatre.

## Scope and responsibility

All projects **MUST**:

- identify an owner for security decisions and vulnerability triage;
- classify their [risk profile](../README.md#applicability-and-proportionality);
- apply the controls relevant to their data, entry points, platforms, privileges, and deployment;
- document approved [exceptions](../README.md#exceptions); and
- avoid claims that imply perfect security.

Public exposure, user accounts, sensitive data, code execution, uploads, multi-tenancy, privileged infrastructure access, or security-critical functionality normally make a project at least **Standard** risk.

## Threat modeling and architecture

A project with meaningful security boundaries **MUST** maintain a threat model using [the template](../templates/threat-model.md) or equivalent.

The model **MUST** cover assets, actors, entry points, data flows, trust boundaries, external dependencies, abuse cases, mitigations, residual risk, and verification. It **MUST** be updated for material changes rather than treated as a one-time launch document.

High-risk design assumptions **SHOULD** be tested with proof-of-concept validation before the architecture depends on them.

## Treat external input as untrusted

External input includes HTTP requests, CLI arguments, files, IPC, database content, environment variables, message queues, webhooks, DNS answers, third-party APIs, browser storage, and data written by older software versions.

- Input **MUST** be validated at the trusted boundary against an explicit schema, type, length, range, format, and allowed values.
- Validation **MUST** occur server-side or inside the trusted process even when the client also validates.
- Inputs **SHOULD** be normalized once before validation when multiple encodings or representations are accepted.
- Size, depth, count, timeout, and resource limits **MUST** exist where input can exhaust memory, CPU, storage, connections, or downstream quotas.
- Sanitization **MUST NOT** be used as a universal substitute for safe APIs, parameterization, contextual encoding, or authorization.

Detailed data, URL, path, and upload rules are in [Data, Input, and Files](data-input-files.md).

## Injection prevention

Data **MUST** remain data when passed to an interpreter.

- Database operations containing untrusted values **MUST** use parameterized queries, prepared statements, or safe query-builder binding.
- String-concatenated SQL containing untrusted input **MUST NOT** be used.
- Command execution **MUST** use direct process APIs with an argument array. Untrusted input **MUST NOT** be interpolated into a shell command.
- Dynamic identifiers such as sort columns that cannot be parameterized **MUST** come from a strict server-controlled allowlist.
- Template, LDAP, NoSQL, expression-language, and code-generation interfaces **MUST** use their safe binding or encoding mechanisms.
- If interpretable content is an intentional feature, it **MUST** run inside a boundary designed for hostile code with resource and privilege limits.

Escaping is context-specific and **SHOULD** be a last line of defense when a safer non-interpreting API is unavailable.

## Authentication, sessions, and authorization

Identity requirements are defined in [Identity and Access](identity-access.md).

At baseline:

- Authentication **MUST NOT** be treated as authorization.
- Authorization **MUST** be enforced at the trusted service for every protected action and object.
- UI visibility, client-side routes, hidden fields, or guessed-unlikely identifiers **MUST NOT** grant or prove permission.
- Access decisions **MUST** default to deny when policy, identity, or context is missing or invalid.
- Privilege changes and sensitive actions **SHOULD** require recent authentication or step-up authentication.
- Session and token revocation **MUST** be possible when compromise or account state requires it.

## Object and tenant authorization

Every request for a user-, organization-, or tenant-owned object **MUST** verify the caller's action on that specific object within the current tenant or security context.

Random UUIDs MAY reduce enumeration, but **MUST NOT** replace authorization. Tenant context **MUST** come from trusted identity or server-side state, not solely from a client-supplied identifier.

Tests **MUST** attempt horizontal and vertical privilege escalation for authorization-sensitive endpoints.

## Browser and web controls

### Cross-site scripting

- Untrusted content **MUST** be rendered through framework-safe text binding or context-appropriate output encoding.
- Unsafe HTML rendering, DOM sinks, and template bypasses **MUST NOT** receive untrusted data without a reviewed sanitizer designed for that content type.
- Content Security Policy **SHOULD** provide defense in depth for public web applications; it **MUST NOT** replace safe rendering.
- User-controlled URLs **MUST** be restricted to intended schemes and destinations.

### Cross-site request forgery

State-changing browser requests authenticated by ambient credentials such as cookies **MUST** be protected with framework CSRF defenses, same-site controls, and/or validated anti-CSRF tokens appropriate to the architecture.

State changes **MUST NOT** use safe/idempotent methods such as GET. CORS **MUST NOT** be treated as CSRF protection.

### Cookies and browser storage

Session cookies **MUST** use `Secure` and `HttpOnly` where applicable and an intentional `SameSite` policy. Scope **SHOULD** be limited by domain, path, and lifetime.

Long-lived credentials **SHOULD NOT** be stored in JavaScript-accessible browser storage. If browser storage is used, the threat model **MUST** address XSS and device compromise.

### CORS and security headers

CORS origins, methods, headers, and credential use **MUST** be explicitly allowlisted. A wildcard origin **MUST NOT** be combined with credentials.

Public web services **SHOULD** set applicable transport, framing, content-type, referrer, and permissions policies through a reviewed baseline.

## Server-side request forgery

When a service retrieves user-influenced URLs:

- allowed schemes and, where feasible, destinations **MUST** be allowlisted;
- credentials **MUST NOT** be forwarded unless explicitly required for the validated destination;
- loopback, link-local, private, metadata, and internal address ranges **MUST** be blocked unless the feature specifically requires them;
- DNS resolution and redirects **MUST** be handled so validation cannot be bypassed by rebinding or a redirect;
- connect, response, size, and total time limits **MUST** be applied; and
- the outbound client **SHOULD** run with constrained network access.

An “internal” URL is not inherently trusted.

## Abuse resistance and rate limiting

Rate, concurrency, size, quota, and cost controls **MUST** exist where realistic abuse could affect authentication, password reset, account creation, verification, uploads, search, messaging, exports, expensive computation, or third-party billing.

Controls **SHOULD** combine relevant identities such as account, credential, IP range, device, tenant, and global capacity. They **MUST NOT** create an easy denial of service against a victim account without considering recovery and legitimate bursts.

Security-sensitive limits and lockouts **SHOULD** be observable and return non-sensitive errors.

## Error handling and failure behavior

- Unhandled failures **MUST** fail closed for protected actions.
- Production responses **MUST NOT** expose stack traces, secret values, connection strings, filesystem layouts, internal queries, or unnecessary dependency versions.
- Errors **SHOULD** include a safe correlation identifier.
- Detailed diagnostics **MAY** be logged internally under the logging rules below.
- Retries **MUST** be bounded and used only for operations that are safe to retry or protected by idempotency.

## Security logging

Projects **MUST** log enough context to investigate applicable:

- authentication success/failure and session lifecycle;
- authorization denials and privilege changes;
- administrative and security-setting changes;
- credential issue, rotation, revocation, and recovery;
- validation failures indicative of attack;
- dependency, integrity, and deployment failures; and
- high-impact data export, deletion, or access events.

Logs **MUST NOT** contain passwords, password reset secrets, raw session or access tokens, private keys, API secrets, full connection strings, or unnecessary sensitive personal data.

Events **SHOULD** use structured fields, UTC timestamps, actor/service identity, safe request or trace ID, action, target, outcome, and source. Audit events **SHOULD** be protected from ordinary user modification.

See [Operations](../infrastructure/operations.md) for retention, access, and alerting.

## Dependencies and software supply chain

Every project **MUST**:

- use the ecosystem's lockfile or equivalent deterministic resolution where available;
- review new direct dependencies for maintenance, publisher, permissions, transitive weight, license, and known vulnerabilities;
- enable automated vulnerability alerts or scheduled scanning;
- remove unused dependencies;
- update supported dependencies through tested pull requests; and
- know which source revision and workflow produced each release artifact.

CI actions, plugins, build containers, installers, and package registries are dependencies too.

Third-party CI actions **SHOULD** be pinned to immutable full commit identifiers and updated through review. Installation scripts fetched from the network **MUST NOT** be piped directly into a privileged shell in release or production automation without verified integrity and review.

High-risk or broadly distributed products **SHOULD** produce SBOMs and provenance/attestations as described in [Release Standards](../lifecycle/release.md).

## Automated security analysis

CI or scheduled automation **MUST** run the checks that materially apply:

| Check | Minimum expectation |
| --- | --- |
| Secret scanning | All repositories; push protection when available |
| Dependency scanning | Lockfiles, manifests, and release dependencies |
| SAST | Languages and frameworks with a useful maintained ruleset |
| Container scanning | Images intended for deployment or distribution |
| IaC/configuration scanning | Material infrastructure-as-code and deployment configuration |
| Dynamic/API testing | Internet-facing or high-risk services where it gives meaningful coverage |

Findings **MUST** be triaged for reachability, impact, exploitability, and available mitigation. A scanner's absence of findings is not proof of security. Suppressions **MUST** include rationale and review context.

## Vulnerability handling

Public or distributed projects **MUST** publish a private vulnerability reporting route, normally in `SECURITY.md`. Reports **MUST** receive acknowledgement and initial triage promptly.

When a serious vulnerability is found:

1. limit further exposure and preserve relevant evidence;
2. identify affected versions, deployments, data, and credentials;
3. contain or disable the vulnerable path when necessary;
4. develop and review the smallest safe remediation;
5. rotate compromised or plausibly exposed credentials;
6. test and release through an emergency or normal gate;
7. notify affected users with actionable information; and
8. document root cause and prevention.

Release-blocking severity **MUST** consider actual product context, not a numeric score alone. Known exploitable critical or high-impact vulnerabilities **MUST NOT** ship without explicit founder-approved risk acceptance and effective compensating controls.

## Security review before production

Before first production release and after material boundary changes:

- the implemented data flow **MUST** match the threat model;
- trust boundaries, authentication, authorization, tenancy, and admin paths **MUST** be reviewed;
- secrets, cryptography, logging, errors, input handling, outbound requests, dependencies, and deployment defaults **MUST** be reviewed where applicable;
- negative tests **MUST** cover the highest-risk abuse cases; and
- unresolved findings and residual risk **MUST** be visible to the release owner.

Use the [release checklist](../templates/release-checklist.md) for evidence. A review is an engineering activity, not a ceremonial approval.
