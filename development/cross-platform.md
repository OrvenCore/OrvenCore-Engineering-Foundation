# Cross-Platform Engineering

> **Status:** Approved — 2026-08-15

Where technically feasible, executable OrvenCore applications **MUST** support **Windows, macOS, and Linux**. Support is an architecture and delivery commitment, not a late packaging exercise.

## Platform commitment

During requirements, every executable product **MUST** declare:

- supported operating systems and minimum versions;
- supported CPU architectures where relevant;
- installation, update, and uninstallation method;
- known feature differences; and
- how each claimed platform is tested.

If one of Windows, macOS, or Linux is technically infeasible or incompatible with the product's purpose, the project **MUST** document the reason and affected capability. A temporary exclusion caused by schedule, staffing, or missing validation requires a time-bound [OcEF exception](../README.md#exceptions). Lack of testing on the developer's secondary OS is not by itself a technical limitation.

Platform-specific products MAY exist when their value inherently depends on an OS capability.

## Isolate platform behavior

OS-specific behavior **MUST** live behind a small, explicit interface or adapter. Common business, security, data, and protocol logic **SHOULD** remain shared.

```text
common application logic
        ↓
platform interface
   ↙      ↓      ↘
Windows  macOS  Linux
```

Call sites **SHOULD NOT** contain scattered platform checks when one adapter can own the difference.

## Filesystem paths and locations

- Applications **MUST** use platform path APIs and joins.
- Windows drive paths such as `C:\...` and Unix paths such as `/home/...` **MUST NOT** be hardcoded for user data or configuration.
- Home-directory environment variables **MUST NOT** be assumed present or equivalent across platforms.
- User configuration, data, cache, state, logs, and temporary files **SHOULD** use platform-appropriate known-folder APIs or a maintained portability library.
- On Linux, desktop applications **SHOULD** respect the XDG Base Directory specification.
- On macOS, applications **SHOULD** use the appropriate `Library` locations.
- On Windows, applications **SHOULD** use Known Folder APIs or the correct roaming/local application-data location.
- Install resources **MUST** be located relative to the installed application or package API, not the current working directory.

Path handling **MUST** account for different separators, roots, UNC/network paths where supported, reserved names, maximum lengths, links, and permissions.

## Case, Unicode, and filenames

- Code **MUST NOT** assume filesystems are case-sensitive or case-insensitive.
- Repository paths that differ only by case **MUST NOT** be used.
- User-facing text and filenames **SHOULD** use Unicode-aware APIs and explicit UTF-8 at portable data boundaries.
- Normalization differences **MUST** be considered when filenames or identifiers are security-relevant.
- Application-generated filenames **SHOULD** avoid characters invalid or awkward on any supported platform.

## Configuration and environment

- Configuration precedence **MUST** be documented, for example: defaults → system config → user config → environment → CLI.
- Environment-variable names **SHOULD** be stable and use the `ORVEN_<PRODUCT>_` prefix.
- Environment-variable values **MUST** be parsed and validated; non-empty does not mean true.
- Configuration files **SHOULD** use a portable, versioned text format unless secrecy or platform integration requires otherwise.
- Secrets **MUST** follow [Secrets and Cryptography](../security/secrets-cryptography.md) rather than relying on the portability of environment variables.

## Shells and subprocesses

- Applications **MUST NOT** assume Bash, `sh`, PowerShell, `cmd.exe`, or a particular terminal exists unless the product explicitly requires and documents it.
- Subprocesses **MUST** use direct process APIs with an argument list rather than a shell string.
- Shell invocation **MUST** be isolated, justified, and safe from untrusted interpolation.
- Executable discovery **SHOULD** use platform APIs or controlled search paths.
- Exit codes, standard streams, timeouts, cancellation, encoding, signal/control-event behavior, and process-tree termination **MUST** be handled deliberately.
- Background operation **SHOULD** use native service/launch mechanisms or a portability layer instead of copying one OS's daemon model.

## Permissions, identities, and secure storage

- Unix mode bits **MUST NOT** be assumed to exist on Windows.
- Windows ACL semantics **MUST NOT** be reduced to Unix mode-bit assumptions.
- The application **SHOULD** run without administrator/root privileges.
- Privilege elevation **MUST** be limited to a separate, explicit operation.
- Credentials **SHOULD** use Keychain on macOS, Credential Manager/DPAPI-backed storage on Windows, and an appropriate desktop keyring or secret service on Linux when available.
- A missing secure-store integration **MUST NOT** cause silent plaintext storage.

## File locking, atomicity, and concurrency

- Lock semantics **MUST** be tested on every supported platform and relevant filesystem type.
- Advisory locks **MUST NOT** be assumed mandatory.
- Atomic replacement **SHOULD** use platform APIs and be verified for the target filesystem.
- Temporary files **MUST** be securely created on the same filesystem when atomic rename is required.
- Sync/backup products **MUST** define behavior for open files, links, sparse files, permissions, timestamps, case collisions, and network filesystems.

## Line endings and executable bits

- Repositories **SHOULD** use `.gitattributes` to make text normalization explicit.
- Source and portable configuration **SHOULD** use LF unless an ecosystem requires CRLF.
- Parsers **SHOULD** accept platform line endings where user-authored input is expected.
- Release tooling **MUST** preserve required Unix executable bits and Windows script/package conventions.
- Generated archives **SHOULD** have deterministic metadata and correct platform permissions.

## Networking

- Code **MUST NOT** assume IPv4 only, `localhost` resolution order, a fixed interface name, or a uniform proxy environment.
- Proxy, certificate-store, DNS, and firewall behavior **SHOULD** use platform conventions.
- Binding to all interfaces **MUST** be an explicit security decision.
- Local IPC **MUST** apply authentication/authorization and restrictive permissions appropriate to named pipes, Unix sockets, loopback TCP, or other mechanisms.

## Desktop UI and integration

Desktop products **SHOULD**:

- follow native keyboard, accessibility, scaling, notification, file-picker, and window conventions;
- support high-DPI and fractional scaling;
- expose visible keyboard focus and accessible labels;
- avoid embedding privileged operations in the UI process;
- handle dark and light appearance intentionally; and
- use official OrvenCore brand assets without recreating the wordmark.

Web content **SHOULD** target WCAG 2.2 AA. Native applications **SHOULD** provide equivalent platform accessibility behavior even when WCAG is not the direct conformance model.

## Packaging, signing, and updates

Each supported platform **MUST** have a documented, repeatable packaging process.

- Windows installers/binaries **SHOULD** be code-signed.
- macOS releases **SHOULD** be signed and notarized.
- Linux packages **SHOULD** use repository/package signatures or verified release checksums appropriate to the distribution method.
- Update mechanisms **MUST** verify artifact integrity and origin before installation.
- Uninstall **SHOULD** preserve or remove user data only according to a clear user choice and documented behavior.
- Platform packages for one version **MUST** identify the same source revision and public product version.

Signing and notarization prove publisher and integrity, not absence of vulnerabilities.

## CI and release validation

A supported platform is not complete until CI or a controlled test environment verifies:

- clean checkout and dependency setup;
- build and automated tests;
- package/installer generation;
- clean install and first launch;
- core user and security flows;
- configuration/data location;
- update from the previous supported release; and
- uninstall or removal behavior when provided.

Failures unique to one OS are product defects, not acceptable drift. Temporary platform exclusions require an [OcEF exception](../README.md#exceptions) and honest release notes.
