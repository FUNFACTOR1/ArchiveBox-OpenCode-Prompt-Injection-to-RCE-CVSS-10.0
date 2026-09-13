# ArchiveBox OpenCode — Indirect Prompt Injection to Arbitrary Command Execution

**Advisory identifier:** CAN-2026-2036559 (MITRE, state: PUBLISHED)
**CVSS 4.0:** `CVSS:4.0/AV:N/AC:L/AT:N/PR:N/UI:N/VC:H/VI:H/VA:H/SC:H/SI:H/SA:H` — **10.0 CRITICAL**
**Classification:** CWE-1426 (Improper Validation of Generative AI Output) + CWE-79 (Cross-site Scripting) + CWE-77 (Command Injection)
**Vendor:** ArchiveBox
**Product:** ArchiveBox
**Affected:** All builds containing the `archivebox/opencode/` module
**Researcher / Finder:** Zago Zampier ([FUNFACTOR1](https://github.com/FUNFACTOR1)) — Section 1, Department of Cyber Security, PS 1978 Limited
**Assignment:** MITRE CNA-LR
**Disclosure date:** 13 September 2026

---

## Summary

ArchiveBox, in builds that include the OpenCode AI agent integration, is vulnerable to indirect prompt injection that escalates to arbitrary command execution on the host operating system. The vulnerability arises because content ingested from untrusted external sources by the platform's archival pipeline enters the AI agent's execution context without trust-boundary marking. No authentication or credentials on the target instance are required. The vulnerability is scored 10.0 CRITICAL under CVSS 4.0 by MITRE.

This advisory is being published through MITRE assignment following the failure of coordinated disclosure through the vendor's own security channel. Full technical detail — file paths, code references, exploit chain — is deferred until MITRE completes the transition of this record from CAN-2026-2036559 to its final CVE identifier, and is being withheld from this disclosure to prevent uplift to attackers ahead of any remediation.

---

## Affected Products

| Field | Value |
|---|---|
| Vendor | ArchiveBox |
| Product | ArchiveBox |
| Component | `archivebox/opencode/` module |
| Affected versions | All builds containing the OpenCode module (merged into the `dev` branch between 2026-08-28 and 2026-09-04) |
| Reference version | 0.9.35rc433 |
| Unaffected | Builds that do not contain the `archivebox/opencode/` module |

---

## Impact

Successful exploitation grants the attacker arbitrary command execution on the host operating system with the privileges of the ArchiveBox process. This includes read of the ArchiveBox database and any file readable by the process; modification or deletion of any file writable by the process; persistent implant through modification of files under the process's working directory; lateral movement to any resource reachable from the host with the process's network posture; and denial of service through process termination or data destruction.

The attacker's only precondition is that the victim archives a page under attacker control and subsequently interacts with the archived collection through the AI agent — which is the agent's designed primary function.

The CVSS 4.0 vector reflects: network attack vector, low attack complexity, no attack requirements, no privileges required, no user interaction, high confidentiality/integrity/availability impact on both the vulnerable system and subsequent systems.

---

## Deployment Context

ArchiveBox is deployed in operational contexts that include journalism, open-source intelligence work by law enforcement, and institutional archival programs, and is referenced in technical conference curricula covering OSINT tooling. In these contexts, the integrity of archived collections and the isolation of untrusted archived content from the host operating system are foundational security properties. This advisory's classification and score reflect the failure of both properties in the affected configuration.

---

## Prior History and Disclosure Timeline

This vulnerability is the composition of a previously reported input-side primitive (the platform's ingestion of unsanitized third-party HTML into contexts where it can act) with a subsequently merged AI agent integration that grants that content a new execution path.

The underlying XSS primitive was reported to the vendor twice before the OpenCode module reached the `dev` branch:

- **10 June 2026** — [GHSA-32m2-xhwx-92mh](https://github.com/ArchiveBox/ArchiveBox/security/advisories/GHSA-32m2-xhwx-92mh) submitted. The maintainer accepted the report on 14 June, committed a fix to `dev`, and closed the advisory.
- **18 June 2026** — [GHSA-h9qq-w7hq-7rxj](https://github.com/ArchiveBox/ArchiveBox/security/advisories/GHSA-h9qq-w7hq-7rxj) submitted, demonstrating that stable releases 0.7.3 and 0.7.4 remained vulnerable to the same primitive with a same-origin admin-mutation chain. A ready-to-merge patch was provided by the researcher. The maintainer declined to apply the patch to stable releases and declined to issue a CVE, citing project focus on the 0.9.x architecture.

Between 10 June 2026 and the merge of the OpenCode module into the `dev` branch beginning 28 August 2026, the maintainer completed the engineering work required to ship the AI agent integration. During the same period, the XSS primitive on stable releases 0.7.3 and 0.7.4 received no patch. The AI agent, once merged, granted the pre-existing unsanitized-content ingestion path a direct route to command execution on the host, producing the vulnerability that is the subject of this advisory.

- **28 August 2026** — MITRE CVE record reserved for the composed vulnerability (CAN-2026-2036559).
- **7 September 2026** — CAN-2026-2036559 published by MITRE with CVSS 4.0 base score 10.0 CRITICAL.
- **13 September 2026** — This public advisory issued.

---

## Basis for Public Disclosure Through MITRE

Two attempts at coordinated disclosure of the underlying primitive through the vendor's own security channel produced no patch for the affected stable releases. Following those attempts, three artifacts on the public record are relevant to the decision to route this advisory through MITRE assignment rather than through further vendor-side coordination.

**1. Retroactive rewriting of the project's security policy.** On 15 June 2026 — the day after the acceptance of the first XSS report on 14 June — commits [`c076737`](https://github.com/ArchiveBox/ArchiveBox/commit/c076737e4ab264cfacf38c7a66f5a31540a6d593) and [`3a542f9`](https://github.com/ArchiveBox/ArchiveBox/commit/3a542f9b34c0634331a2c01acf5c1bf93f59f05e) rewrote `.github/SECURITY.md` to add three sections that had not previously existed in the file's history since its creation in October 2024: a CVE policy stating that reports affecting pre-release, `dev`, or beta versions would not receive CVEs; a threat model declaring that all admin users are trusted; and an "XSS, CSRF, CORS, CSP" section stating verbatim: *"Do not open advisories related to XSS or CSRF for those versions"* (`< v0.9.x`). Subsequent commits on 21 June, 15 July, and 28 July extended the retroactive scope to include SSRF.

**2. Retroactive modification of a prior CVE record.** On 7 July 2026 — 19 days after the researcher disclosed the same-origin XSS-to-admin-mutation chain via GHSA-h9qq-w7hq-7rxj — the CNA (GitHub, Inc.) modified [CVE-2023-45815](https://nvd.nist.gov/vuln/detail/CVE-2023-45815). The original October 2023 advisory was classified as CWE-79 only, scored CVSS 5.4 with confidentiality Low, integrity Low, availability None, and prescribed the mitigation `SAVE_WGET=False`.

**3. A prior contradictory precedent by the same maintainer.** The security policy added on 15 June 2026 states that pre-release versions do not receive CVEs. On 23 April 2026 — approximately seven weeks earlier — the maintainer self-requested and published [CVE-2026-42601](https://nvd.nist.gov/vuln/detail/CVE-2026-42601) (GHSA-3h23-7824-pj8r) for ArchiveBox `<= 0.8.6rc0`, which is a release candidate. The policy invoked to decline CVE assignment for the researcher's reports on stable releases 0.7.3 and 0.7.4 was contradicted by the maintainer's own conduct on a pre-release version of the same product within the same calendar quarter.

Coordinated disclosure through the vendor's channel was exhausted. Public assignment through MITRE is the route being used for this advisory.

---

## References

All references correspond to those recorded in the MITRE CVE record.

1. Official product repository — https://github.com/ArchiveBox/ArchiveBox
2. Dev branch commit at the time of disclosure, showing the agent module in tree — https://github.com/ArchiveBox/ArchiveBox/commit/603bc01fa82aa815aa1c0b2f70e95fc55c6eb386
3. Pull request allowing same-origin framing for the embedded AI agent UI — https://github.com/ArchiveBox/ArchiveBox/pull/1855
4. Pull request routing authenticated OpenCode WebSockets and terminal integration — https://github.com/ArchiveBox/ArchiveBox/pull/1870
5. Prior advisory on the underlying XSS primitive — https://github.com/ArchiveBox/ArchiveBox/security/advisories/GHSA-32m2-xhwx-92mh
6. Prior advisory on the underlying XSS primitive (stable release escalation) — https://github.com/ArchiveBox/ArchiveBox/security/advisories/GHSA-h9qq-w7hq-7rxj
7. MITRE CVE record — https://www.cve.org/CVERecord?id=CAN-2026-2036559

---

## Full Technical Audit

The complete source-level audit — file paths, function references, line numbers verified against the dev-branch commit at the time of disclosure, the agent instruction template, the pseudo-terminal endpoint flow, and the end-to-end data path from external HTML ingestion to command execution — is retained by the researcher and will be released as an amendment to this repository following MITRE's transition of this record from `CAN-2026-2036559` to its final `CVE-2026-XXXXX` identifier.

Withholding the technical audit until CVE publication is a deliberate disclosure choice by the researcher, not a limitation. The information sufficient for defenders to identify affected deployments (the presence of the `archivebox/opencode/` module in the installed build) and to mitigate (removal of that module closes the vulnerability) is stated in this document.

---

## Mitigation

Removal of the `archivebox/opencode/` module from any affected build closes the vulnerability. No configuration change within the module neutralizes the vector.

---

## Credits

**Finder:** Zago Zampier (Zampier Zago) — https://github.com/FUNFACTOR1
Section 1, Department of Cyber Security, PS 1978 Limited
