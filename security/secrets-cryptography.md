# Secrets and Cryptography

> **Status:** Approved — 2026-08-15

Secrets and cryptographic keys are lifecycle-managed assets. Encryption is useful only when the keys, endpoints, and operational processes are protected.

## What is a secret

Secrets include:

- API, access, refresh, session, and webhook tokens;
- database, SMTP, service, and infrastructure credentials;
- OAuth client secrets;
- private signing, encryption, TLS, and SSH keys;
- recovery codes and bootstrap credentials;
- CI/CD and package-registry credentials; and
- any value that grants access or proves identity.

Public identifiers, public keys, and ordinary configuration are not secrets, though integrity may still matter.

## Non-negotiable rules

- Secrets **MUST NOT** be committed to source control, examples, test fixtures, container images, build artifacts, documentation, issue trackers, or logs.
- Real credentials **MUST NOT** be used in automated tests.
- A secret accidentally committed or included in an artifact **MUST** be treated as compromised.
- The exposed credential **MUST** be revoked or rotated. Deleting it from the latest commit, rewriting history, or making the repository private is not remediation by itself.
- Secrets **MUST NOT** have insecure hardcoded defaults.
- Applications **MUST** fail clearly when a required secret is absent rather than silently using a weak fallback.

History cleanup MAY reduce future discovery, but it happens only after revocation and must not disrupt investigation or collaborators without a plan.

## Secret inventory and ownership

Every production secret **MUST** have:

- a purpose and owner;
- issuing system and affected environment;
- scope and consuming workload;
- storage location or reference;
- rotation and revocation method;
- known dependent services; and
- an audit or last-rotation signal where practical.

The inventory **MUST NOT** contain the secret value itself.

Long-lived, broad, or manually distributed credentials **SHOULD** be replaced with scoped, short-lived, workload-bound credentials where supported.

## Local development

- Local secrets **SHOULD** live in an OS credential store, approved secret manager, or ignored local environment/configuration file.
- Repositories **MAY** include a `.env.example` or sample configuration containing names and safe dummy values only.
- `.env` and local override files **MUST** be ignored before they are populated.
- Developers **MUST NOT** copy production secrets into local development when isolated development credentials or representative fixtures can work.
- Local instructions **SHOULD** explain how to obtain, rotate, and remove development credentials.

Environment variables are a transport mechanism, not a secret manager. Their exposure through process inspection, crash reports, child processes, and diagnostics **MUST** be considered.

## CI/CD

- CI secrets **MUST** use the platform's protected secret mechanism or workload identity.
- Workflow permissions **MUST** default to read-only and be elevated per job.
- Untrusted pull-request code **MUST NOT** receive secrets or privileged tokens.
- Secrets **MUST NOT** be passed through command-line arguments or printed by tracing/debug modes.
- Environments **SHOULD** separate development, staging, and production credentials.
- Production deployments **SHOULD** use short-lived federation and protected environments rather than static cloud or server keys.
- Third-party actions receiving secrets **MUST** be minimized, pinned, and reviewed.

Masking a known value in logs is defense in depth; it is not permission to print the value.

## Production

- Production secrets **MUST** be delivered at runtime or deployment time, never baked into an image.
- Access to secrets **MUST** be limited to the intended workload and administrators who operate the secret system.
- Secret storage **MUST** be encrypted in transit and protected at rest according to the platform threat model.
- Files containing secrets **MUST** use restrictive ownership and permissions.
- Secret material **SHOULD** remain in memory for the shortest practical period and **MUST NOT** be returned by health or debug endpoints.
- Development, staging, and production **MUST NOT** share credentials.

Docker Compose secret references, mounted files, or an external secret manager MAY be used. Plaintext values committed in a Compose file **MUST NOT**.

## Rotation and revocation

Rotation **MUST** be possible without redesigning the application.

- Compromised or plausibly exposed credentials **MUST** be revoked or rotated promptly.
- Rotations **SHOULD** support overlapping current/next credentials where uninterrupted service requires it.
- Consumers **MUST** reload or restart in a documented, observable way.
- Old credentials **MUST** be disabled after validation.
- Rotation events **SHOULD** be audited.

Periodic rotation **SHOULD** be based on credential type, exposure, platform capability, and threat—not ritual alone. Short-lived automatically renewed credentials reduce the need for disruptive calendar rotation.

## Cryptographic decisions

- OrvenCore **MUST NOT** invent cryptographic algorithms, protocols, formats, padding schemes, or key-derivation constructions.
- Implementations **MUST** use maintained, widely reviewed libraries and high-level safe APIs.
- Cryptographic choices **MUST** follow current authoritative platform and standards guidance at implementation time, not stale copied snippets.
- Algorithms, key sizes, protocol versions, and parameters **MUST** be configurable or migratable when reasonable.
- Deprecated algorithms or protocol versions **MUST NOT** be introduced.
- Cryptographic failures **MUST** fail closed and be observable without leaking key material.

If the team cannot explain the required security property, attacker, key owner, rotation path, and failure behavior, encryption **SHOULD NOT** be implemented until the design is reviewed.

## Data in transit

- Sensitive or authenticated network traffic **MUST** use a current secure transport.
- Internet-facing TLS **MUST** support TLS 1.2 or later and **SHOULD** prefer TLS 1.3; TLS 1.0 and 1.1 **MUST NOT** be enabled.
- Certificate validation **MUST NOT** be disabled in production clients.
- Hostname, issuer chain, validity, and revocation behavior **MUST** use maintained platform mechanisms.
- Internal traffic **MUST NOT** be assumed safe solely because it uses a private network. Encryption and service authentication **SHOULD** follow the threat model and infrastructure boundary.

Certificate pinning MAY be appropriate for narrowly controlled clients, but it **MUST** include a safe rotation and recovery plan.

## Data at rest

Encryption at rest **MUST** be considered for sensitive data, portable devices, backups, and storage outside a tightly controlled boundary.

Where application-layer encryption is used:

- an authenticated-encryption construction from a maintained library **MUST** be used;
- nonces/initialization values **MUST** follow the library's uniqueness and randomness rules;
- associated context **SHOULD** bind ciphertext to its purpose or record where useful;
- keys **MUST** be separated from encrypted data and by environment and purpose;
- key identifiers and versioning **MUST** support rotation; and
- loss of a key and restoration from backup **MUST** be planned.

Full-disk or provider-managed encryption MAY satisfy the threat when the goal is protection of lost media. It does not protect against a compromised running application with access to plaintext.

## Password hashing

Passwords **MUST** be hashed, not encrypted. Use a current salted, adaptive, memory-hard password-hashing method through a maintained library and parameters reviewed against current [OWASP guidance](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html).

- Each password **MUST** have a unique salt generated by the library.
- A pepper MAY provide defense in depth only when stored separately in a secret manager and rotation is planned.
- Hash parameters **SHOULD** be stored with the hash and upgraded over time.
- General-purpose fast hashes **MUST NOT** be used for password storage.

See [Identity and Access](identity-access.md) for password-verifier behavior.

## Randomness, identifiers, and comparison

- Keys, tokens, session identifiers, reset codes, nonces, and other security values **MUST** use a cryptographically secure random source.
- General-purpose pseudo-random generators, timestamps, sequential IDs, and UUID variants without sufficient unpredictability **MUST NOT** create secrets.
- Security-sensitive equality checks **SHOULD** use constant-time comparison through a library where timing exposure is realistic.
- Generated secret values **MUST** have sufficient entropy for their lifetime, attempt rate, and attacker access.

## Key management and signing

Cryptographic keys **MUST** have purpose, owner, environment, access policy, backup/recovery decision, rotation, and revocation.

- One key **SHOULD NOT** serve unrelated signing and encryption purposes.
- Private keys **MUST NOT** be exported or copied more broadly than required.
- Release signing **SHOULD** use hardware-backed or managed key protection where practical.
- Verification keys and trust roots **MUST** have an authenticated distribution and update path.
- Key compromise **MUST** trigger revocation, impact analysis, reissuance, and dependent artifact or data review.

Signing proves origin and integrity under a key; it does not prove the signed software is safe.

## Logging and diagnostics

Applications, proxies, CI, exception trackers, and support bundles **MUST** redact secrets before storage or transmission.

Redaction **SHOULD** use field-aware allowlisting rather than broad regular expressions alone. Diagnostics **MUST** be tested with representative secret-bearing inputs. Access to secret-related audit logs **SHOULD** be restricted because metadata can still be sensitive.
