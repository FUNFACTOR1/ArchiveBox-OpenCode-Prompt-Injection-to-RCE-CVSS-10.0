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
<img width="1448" height="1086" alt="ChatGPT Image 17 set 2026, 18_01_54" src="https://github.com/user-attachments/assets/c9e5d0fd-50ca-413f-a234-dffd86f08d02" />


## Summary

ArchiveBox, in builds that include the OpenCode AI agent integration, is vulnerable to indirect prompt injection that escalates to arbitrary command execution on the host operating system. The vulnerability is caused by the same-origin embedding of a privileged remote web UI into the web application, along with an unsafe instruction execution channel between the archived collection and the tool-using agent. A malicious page saved to ArchiveBox and later viewed through the AI agent can cause the model to execute attacker-controlled shell commands on the host with the privileges of the ArchiveBox process.

This advisory is being published through MITRE assignment following the failure of coordinated disclosure through the vendor's own security channel. Full technical detail — file paths, code references, and exploitation steps — is intentionally withheld until formal publication.

---

## Affected Products

| Field | Value |
|---|---|
| Vendor | ArchiveBox |
| Product | ArchiveBox |
| Component | `archivebox/opencode/` module |
| Affected versions | All builds containing the OpenCode module (merged into the `dev` branch between 2026-08-28 through 2026-10-05 (latest available release; code invariance verified across two complete architectural refactorings)) |
| Reference version | 0.9.72rc97 (HEAD ccaa6308, abx-plugins[opencode]==1.13.147) |
| Unaffected | Builds that do not contain the `archivebox/opencode/` module |

---

## Impact

Successful exploitation grants the attacker arbitrary command execution on the host operating system with the privileges of the ArchiveBox process. This includes read of the ArchiveBox database and filesystem, modification of archival state and user data, pivoting into adjacent services reachable from the host, and persistence by writing files or scheduled jobs.

The recursive synergy is particularly severe: the archived content is not merely rendered to the user, but is also inserted into the agent's working memory and tool call context as if it were trusted external input. Since the agent is connected to a full terminal session, the same malicious content becomes a command execution primitive with no upstream validation boundary.

The CVSS 4.0 vector reflects: network attack vector, low attack complexity, no attack requirements, no privileges required, no user interaction, high confidentiality/integrity/availability impact, and impact on the system's security scope.

---

## Deployment Context

ArchiveBox is deployed in operational contexts that include journalism, open-source intelligence work by law enforcement, and institutional archival programs, and is referenced in technical conference material as a tool for preserving and querying large web collections. The same deployment posture that makes the project useful also creates a high-risk trust boundary: the host runs a privileged local agent for archived content that is not itself authenticated or verified as trustworthy.

---

## Prior History and Disclosure Timeline

This vulnerability is the composition of a previously reported input-side primitive (the platform's ingestion of unsanitized third-party HTML into contexts where it can act) with a subsequently merged AI integration. The malware scenario is therefore not a hypothetical edge case but a direct consequence of the combination of the browser and execution surfaces.

The underlying XSS primitive was reported to the vendor twice before the OpenCode module reached the `dev` branch:

- **10 June 2026** — [GHSA-32m2-xhwx-92mh](https://github.com/ArchiveBox/ArchiveBox/security/advisories/GHSA-32m2-xhwx-92mh) submitted. The maintainer accepted the report on 14 June, committed a fix, and subsequently attempted to limit the scope through project policy.
- **18 June 2026** — [GHSA-h9qq-w7hq-7rxj](https://github.com/ArchiveBox/ArchiveBox/security/advisories/GHSA-h9qq-w7hq-7rxj) submitted, demonstrating that stable releases 0.7.3 and 0.7.4 remained vulnerable after the first fix.

Between 10 June 2026 and the merge of the OpenCode module into the `dev` branch beginning 28 August 2026, the maintainer completed the engineering work required to ship the AI agent integration. The vulnerability became reachable only after the isolated XSS primitive and the agent execution feature were combined in the same product build.

- **28 August 2026** — MITRE CVE record reserved for the composed vulnerability (CAN-2026-2036559).
- **7 September 2026** — CAN-2026-2036559 published by MITRE with CVSS 4.0 base score 10.0 CRITICAL.
- **13 September 2026** — This public advisory issued.

---

## Basis for Public Disclosure Through MITRE

Two attempts at coordinated disclosure of the underlying primitive through the vendor's own security channel produced no patch for the affected stable releases. Following those attempts, three additional facts made public assignment necessary:

**1. Retroactive rewriting of the project's security policy.** On 15 June 2026 — the day after the acceptance of the first XSS report on 14 June — commits were introduced that narrowed the class of issues eligible for security handling.

**2. Retroactive modification of a prior CVE record.** On 7 July 2026 — 19 days after the researcher disclosed the same-origin XSS-to-admin-mutation chain via GHSA-h9qq-w7hq-7rxj — the CNA (GitHub) altered a previously issued record to remove the relevant issue from the security advisory history.

**3. A prior contradictory precedent by the same maintainer.** The security policy added on 15 June 2026 states that pre-release versions do not receive CVEs. On 23 April 2026 — approximately several months earlier — the maintainer had already issued a CVE for a pre-release build.

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

The complete source-level audit — file paths, function references, line numbers verified against the dev-branch commit at the time of disclosure, the agent instruction template, the pseudo-terminal execution path, and the privilege escalation chain — is intentionally withheld pending the conclusion of the MITRE assignment process.

Withholding the technical audit until CVE publication is a deliberate disclosure choice by the researcher, not a limitation. The information sufficient for defenders to identify affected deployments is included above.

---

## Mitigation

Removal of the `archivebox/opencode/` module from any affected build closes the vulnerability. No configuration change within the module neutralizes the vector.

---

## Section 9 — Disclosure Status

This advisory is released under full disclosure following the documented failure of coordinated vulnerability disclosure with the vendor over a period of 118 days.

Coordinated disclosure was initiated on 10 June 2026 and systematically exhausted all available channels: direct vendor contact (GHSA-32m2-xhwx-92mh, accepted 14 June 2026), CISA VINCE, and the maintained security endpoint for the project.

During the coordination period, the vendor demonstrated a pattern of systematic obstruction: retroactive rewriting of the security policy to exclude reported vulnerability classes (15 June 2026), and the alteration of prior public vulnerability records to reduce the effective disclosure trail.

Public advisory issued 13 September 2026. Forensic report finalised 5 October 2026 on the latest available release HEAD ccaa6308 (v0.9.72rc97, abx-plugins[opencode]==1.13.147), confirming code invariance across two complete architectural refactorings.

Vulnerability Discovery & Lead Security Researcher: Ing. Zampier Zago
Organisation & Department: SESSION1 Department of Cyber Security, PS 1978 Limited, London, United Kingdom
Official Contact: info@ps1978ltd.it | GitHub: FUNFACTOR1
Advisory Release Date: October 5, 2026

---

## Credits

**Finder:** Zago Zampier (Zampier Zago) — https://github.com/FUNFACTOR1
Section 1, Department of Cyber Security, PS 1978 Limited
