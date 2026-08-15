# References

> **Status:** Approved — 2026-08-15  
> Sources checked: **2026-08-15**

OcEF is an OrvenCore standard, not a substitute for the standards below and not a claim of formal certification. These sources were selected because they materially influenced an OcEF requirement. Product teams **SHOULD** consult the current source when implementing a security-sensitive control.

## OrvenCore sources

- [OrvenCore Brand](https://github.com/OrvenCore/brand) — company identity, principles, audience, naming, Geist typography, and communication voice. OcEF uses the separately approved **Open Joint** production logo assets and their Orven Blue palette in this repository header.

## Requirements language

- [IETF BCP 14: RFC 2119 and RFC 8174](https://www.rfc-editor.org/info/bcp14/) — definitions of uppercase **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY**. RFC 8174 clarifies that the special meanings apply only in uppercase.

## Secure software development

- [NIST SP 800-218, Secure Software Development Framework (SSDF) 1.1](https://csrc.nist.gov/pubs/sp/800/218/final) — current final SSDF baseline for preparing the organization, protecting software, producing well-secured releases, and responding to vulnerabilities. NIST published an initial draft of SSDF 1.2 in 2025; OcEF cites the current final revision rather than treating a draft as mandatory.
- [OWASP Application Security Verification Standard 5.0.0](https://owasp.org/www-project-application-security-verification-standard/) — testable application-security requirements. ASVS influenced the boundaries, validation, authentication, access-control, cryptography, data, communications, and secure-configuration sections.
- [OWASP Top 10:2025](https://owasp.org/www-project-top-ten/) — current web-application risk awareness. OcEF uses it as a risk lens, not as a complete verification standard.
- [OpenSSF OSPS Baseline, current version](https://baseline.openssf.org/) — tiered open-source project controls; the site identifies `v2026.02.19` as current when this edition was researched.

## Practical application-security guidance

The [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/) materially informed the actionable controls below:

- [Input Validation](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html), [SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html), and [Cross-Site Scripting Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html);
- [Authorization](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html), [IDOR Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html), [Authentication](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html), and [Session Management](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html);
- [CSRF Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html) and [SSRF Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Server_Side_Request_Forgery_Prevention_Cheat_Sheet.html);
- [File Upload](https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html) and [Logging](https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html);
- [Secrets Management](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html), [Cryptographic Storage](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html), and [Password Storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html); and
- [Docker Security](https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html) and [Secure Code Review](https://cheatsheetseries.owasp.org/cheatsheets/Secure_Code_Review_Cheat_Sheet.html).

Cheat sheets are implementation guidance. Product teams **MUST** still account for their language, framework, platform, and threat model.

## Identity, OAuth, and transport

- [NIST SP 800-63-4 Digital Identity Guidelines](https://pages.nist.gov/800-63-4/) and [SP 800-63B-4 Authentication and Authenticator Management](https://pages.nist.gov/800-63-4/sp800-63b.html) — current final identity and authenticator lifecycle guidance, including passwords, phishing resistance, recovery, and authenticator assurance.
- [RFC 9700 / BCP 240, Best Current Practice for OAuth 2.0 Security](https://www.rfc-editor.org/info/rfc9700/) — current OAuth security BCP, including PKCE, redirect handling, token protection, and deprecated grant patterns.
- [RFC 8252 / BCP 212, OAuth 2.0 for Native Apps](https://www.rfc-editor.org/info/rfc8252/) — native apps use external user agents and platform-appropriate redirects.
- [IETF BCP 195](https://www.rfc-editor.org/info/bcp195/) — current TLS/DTLS deployment guidance, including the deprecation of TLS 1.0 and 1.1 and secure TLS recommendations.

## Supply chain and release integrity

- [SLSA specification 1.2](https://slsa.dev/spec/v1.2/) — current approved supply-chain levels and attestation/provenance concepts.
- [SPDX specification](https://spdx.dev/use/specifications/) and [CycloneDX specification](https://cyclonedx.org/specification/overview/) — interoperable SBOM formats. OcEF allows either according to ecosystem and consumer needs.
- [GitHub secure use reference for Actions](https://docs.github.com/en/actions/reference/security/secure-use) — least-privilege workflow tokens, untrusted input, dependency review, code scanning, and action/workflow security.
- [GitHub protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches) and [GitHub security features](https://docs.github.com/en/code-security/getting-started/github-security-features) — repository rules, required checks/reviews, dependency review, code scanning, and secret protection.
- [Reproducible Builds definition](https://reproducible-builds.org/docs/definition/) — precise meaning of bit-for-bit reproducibility from declared source, build environment, and instructions. OcEF distinguishes reproducible from merely repeatable builds.

## Containers, operations, and incidents

- [NIST SP 800-190, Application Container Security Guide](https://csrc.nist.gov/pubs/sp/800/190/final) — container image, registry, orchestrator, host, and runtime threats and mitigations.
- [Docker build best practices](https://docs.docker.com/build/building/best-practices/) — maintained primary guidance for minimal contexts, deliberate base-image management, multi-stage builds, and ephemeral containers.
- [NIST SP 800-61 Revision 3, Incident Response Recommendations](https://csrc.nist.gov/pubs/sp/800/61/r3/final) — April 2025 final guidance integrating detection, response, recovery, and continuous improvement with risk management.

## Cross-platform and accessibility

- [XDG Base Directory Specification](https://specifications.freedesktop.org/basedir/) — Linux user data, configuration, state, and cache locations.
- [GitHub-hosted runners](https://docs.github.com/actions/using-github-hosted-runners/about-github-hosted-runners) and [matrix jobs](https://docs.github.com/actions/writing-workflows/choosing-what-your-workflow-does/running-variations-of-jobs-in-a-workflow) — supported Ubuntu, Windows, and macOS CI execution and multi-platform matrices.
- [W3C Web Content Accessibility Guidelines 2.2](https://www.w3.org/TR/WCAG22/) — current W3C Recommendation and OrvenCore's target for accessible web content.

## Versioning

- [Semantic Versioning 2.0.0](https://semver.org/) — version semantics for products with a declared public API or compatibility contract.

## Reference maintenance

When a referenced source is superseded:

1. evaluate whether the new source changes an OcEF requirement;
2. update affected documents and tests together;
3. record any migration or temporary exception required by existing products; and
4. do not silently reinterpret an approved requirement from a draft or secondary summary.
