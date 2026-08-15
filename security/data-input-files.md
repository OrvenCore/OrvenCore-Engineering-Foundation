# Data, Input, and Files

> **Status:** Approved — 2026-08-15

Data handling is a complete lifecycle: collection, validation, use, storage, transmission, export, backup, retention, deletion, and recovery.

## Data inventory and minimization

Before collecting or accepting data, the project **MUST** identify:

- purpose and owner;
- source and intended users;
- sensitivity and likely harm if exposed or altered;
- authoritative store and replicas;
- access and authorization rules;
- retention and deletion behavior;
- backup and restore behavior; and
- third-party processors or destinations.

Projects **MUST** collect and retain only data required for a documented product, security, legal, or operational purpose. “May be useful later” is not sufficient justification for sensitive data.

Use a simple classification appropriate to the product:

| Class | Examples | Baseline handling |
| --- | --- | --- |
| **Public** | Published docs, public releases | Integrity and availability still matter |
| **Internal** | Non-public architecture, routine operational metadata | Authenticated access; do not publish accidentally |
| **Confidential** | Customer records, private project data, credentials metadata | Least privilege, controlled export, retention and audit |
| **Restricted** | Secrets, private keys, highly sensitive personal or security data | Strongest isolation, explicit access, minimal retention; secrets follow [Secrets and Cryptography](secrets-cryptography.md) |

Classification **MUST** be based on impact, not filename or storage location.

## Input contracts

Every external boundary **MUST** define an input contract:

- accepted type, encoding, and schema;
- required and optional fields;
- length, range, count, depth, and total-size limits;
- normalization rules;
- unknown-field behavior;
- timeout and resource budgets; and
- clear rejection behavior.

Client-side validation MAY improve usability but **MUST NOT** replace validation at the trusted boundary.

Validation **SHOULD** allow known-good structures rather than trying to list every malicious string. Input **MUST** be revalidated when crossing into a component with a different trust or type assumption.

## Databases

- Untrusted values in database queries **MUST** use parameter binding.
- String-concatenated SQL containing untrusted input **MUST NOT** be used.
- Dynamic query identifiers **MUST** be selected from a server-controlled allowlist.
- Application database accounts **MUST** have only required schema and operation privileges.
- Administrative/migration credentials **MUST NOT** be used by the routine application process.
- Production databases **MUST NOT** be exposed directly to the public internet without an exceptional, documented design and compensating controls.
- Connections **MUST** use authentication and encrypted transport across untrusted boundaries.
- Query time, result size, and connection-pool limits **SHOULD** prevent resource exhaustion.

Schema changes **MUST** use version-controlled migrations. Migrations **MUST** be tested on representative data, and destructive changes **MUST** include backup/recovery and compatibility analysis. See [Release Standards](../lifecycle/release.md#database-and-state-changes).

Backups **MUST** preserve the confidentiality and integrity requirements of the original data and follow [Operations](../infrastructure/operations.md#backups-and-restore).

## Serialization and structured data

- Untrusted data **MUST NOT** be deserialized into executable object graphs or types that can invoke code.
- Parsers **MUST** disable external entity or remote schema resolution unless specifically required and constrained.
- Structured inputs **MUST** have depth, size, collection, and recursion limits where parser exhaustion is realistic.
- Unknown fields **SHOULD** be rejected for security-sensitive commands and MAY be ignored for explicitly forward-compatible read models.
- Data formats **SHOULD** be versioned when persistence or interoperability outlives one application version.

Signatures on serialized content verify integrity and origin; they **MUST NOT** make unsafe deserialization safe.

## File uploads

If uploads are accepted:

- allowed purpose and file types **MUST** be explicit;
- request, file, count, decompressed-size, and storage limits **MUST** be enforced;
- extensions, declared content type, file signatures, and actual parser behavior **SHOULD** be compared as defense in depth;
- the original filename **MUST NOT** determine the storage path;
- server-generated opaque names **SHOULD** be used;
- uploads **MUST** be stored outside executable and application-source directories;
- uploaded content **MUST NOT** be executable by the application server;
- access to stored files **MUST** be authorization-checked;
- image/document rewriting, malware scanning, quarantine, or human review **SHOULD** be used when product risk justifies it; and
- rejected or abandoned temporary files **MUST** be cleaned up safely.

No finite allowlist or malware scanner guarantees that a file is safe. The processing environment **SHOULD** be isolated and least-privileged.

## Archives and compression

Before extracting an archive:

- entry count, nesting, compression ratio, and expanded size **MUST** be bounded;
- each destination path **MUST** remain inside the intended extraction root;
- absolute paths, traversal, device files, and unsafe links **MUST** be rejected;
- existing files **MUST NOT** be overwritten unless explicitly designed and authorized; and
- extraction **SHOULD** occur in an isolated temporary directory.

Archives from untrusted sources **MUST NOT** be extracted by a privileged process.

## Paths and filenames

- Platform path libraries **MUST** be used; paths **MUST NOT** be built by manual separator concatenation.
- Untrusted absolute paths and traversal segments such as `../` or `..\` **MUST** be rejected when a path is expected to remain under an application root.
- The resolved target **MUST** be verified as a descendant of the intended root after normalization and relevant link resolution.
- Validation **MUST** account for case sensitivity, alternate separators, encoded separators, reserved names, links, mount points, and platform-specific path forms where applicable.
- Race-sensitive filesystem operations **SHOULD** use descriptor/handle-based or atomic platform APIs instead of check-then-use logic.
- Temporary files **MUST** use secure platform creation with unpredictable names and restrictive permissions.

Removing literal `../` substrings is not sufficient traversal protection.

## Downloads and content serving

- Authorization **MUST** be checked at download time for the requested object.
- Server-side identifiers **SHOULD** map to stored objects rather than accept arbitrary filesystem paths.
- Response content type and `Content-Disposition` **MUST** match intended behavior.
- Untrusted active content **SHOULD** be served from an isolated origin or forced as an attachment.
- Filenames in headers **MUST** be safely encoded and free of control/header injection.
- Byte-range, streaming, and export endpoints **SHOULD** have resource and rate limits.

Signed download URLs **MUST** be short-lived, scoped to one object and operation, and protected from logging or disclosure. They do not replace access control before issuance.

## URLs and outbound requests

URLs are structured, security-sensitive input.

- Schemes **MUST** be allowlisted.
- Userinfo, ambiguous encodings, unsupported ports, and malformed hosts **SHOULD** be rejected.
- If destinations are constrained, validation **MUST** consider resolved addresses and every redirect, not only the initial string.
- Loopback, link-local, private, metadata, and internal destinations **MUST** be blocked unless explicitly required.
- DNS rebinding and time-of-check/time-of-use differences **MUST** be considered.
- Requests **MUST** have connection, read, total-time, redirect, response-size, and concurrency limits.
- Outbound credentials **MUST** be attached only to validated intended destinations.

See the [SSRF baseline](baseline.md#server-side-request-forgery).

## External services and remote content

Third-party API responses, webhooks, packages, remote configuration, imported databases, and cached content **MUST** be treated as untrusted even when transported over TLS.

- Response schemas, sizes, status codes, and timeouts **MUST** be validated.
- Signatures **MUST** be verified when authenticity depends on them.
- Replay protection **MUST** exist for signed events where repeat processing is harmful.
- Webhook secrets **MUST** follow [Secrets and Cryptography](secrets-cryptography.md), and authorization **MUST** be based on verified event identity and context.
- Remote failure **MUST NOT** silently produce unsafe local defaults.

## Data integrity and concurrency

Important state changes **SHOULD** be transactional, atomic, idempotent, or compensatable.

- APIs that may be retried **SHOULD** accept scoped idempotency keys or use naturally idempotent operations.
- Concurrent writes **MUST** have an explicit conflict policy.
- Integrity checks **SHOULD** protect artifacts and backups across storage or transfer.
- Partial writes and crash recovery **MUST** be tested for data-critical paths.

## Retention, export, and deletion

- Retention periods or criteria **MUST** be defined for sensitive and operational data.
- Automated deletion **SHOULD** enforce the policy where practical.
- User or administrator exports **MUST** be authorization-checked, rate-limited where abuse is realistic, and delivered securely.
- Deletion behavior **MUST** describe primary stores, replicas, caches, search indexes, logs, and backup expiry.
- Products **MUST NOT** claim immediate complete deletion when backup or legal retention makes that untrue.
- Test data **MUST NOT** contain production personal data unless specifically authorized, minimized, protected, and necessary.

The user-facing description **MUST** match actual technical behavior.
