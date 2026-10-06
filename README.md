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

ArchiveBox, in builds that include the OpenCode AI agent integration, is vulnerable to indirect prompt injection that escalates to arbitrary command execution on the host operating system. The vulnera[...]

This advisory is being published through MITRE assignment following the failure of coordinated disclosure through the vendor's own security channel. Full technical detail — file paths, code referenc[...]

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

Successful exploitation grants the attacker arbitrary command execution on the host operating system with the privileges of the ArchiveBox process. This includes read of the ArchiveBox database and an[...]

The attacker's only precondition is that the victim archives a page under attacker control and subsequently interacts with the archived collection through the AI agent — which is the agent's designe[...]

The CVSS 4.0 vector reflects: network attack vector, low attack complexity, no attack requirements, no privileges required, no user interaction, high confidentiality/integrity/availability impact on b[...]

---

## Deployment Context

ArchiveBox is deployed in operational contexts that include journalism, open-source intelligence work by law enforcement, and institutional archival programs, and is referenced in technical conference[...]

---

## Prior History and Disclosure Timeline

This vulnerability is the composition of a previously reported input-side primitive (the platform's ingestion of unsanitized third-party HTML into contexts where it can act) with a subsequently merged[...]

The underlying XSS primitive was reported to the vendor twice before the OpenCode module reached the `dev` branch:

- **10 June 2026** — [GHSA-32m2-xhwx-92mh](https://github.com/ArchiveBox/ArchiveBox/security/advisories/GHSA-32m2-xhwx-92mh) submitted. The maintainer accepted the report on 14 June, committed a fix[...]
- **18 June 2026** — [GHSA-h9qq-w7hq-7rxj](https://github.com/ArchiveBox/ArchiveBox/security/advisories/GHSA-h9qq-w7hq-7rxj) submitted, demonstrating that stable releases 0.7.3 and 0.7.4 remained vu[...]

Between 10 June 2026 and the merge of the OpenCode module into the `dev` branch beginning 28 August 2026, the maintainer completed the engineering work required to ship the AI agent integration. Durin[...]

- **28 August 2026** — MITRE CVE record reserved for the composed vulnerability (CAN-2026-2036559).
- **7 September 2026** — CAN-2026-2036559 published by MITRE with CVSS 4.0 base score 10.0 CRITICAL.
- **13 September 2026** — This public advisory issued.

---

## Basis for Public Disclosure Through MITRE

Two attempts at coordinated disclosure of the underlying primitive through the vendor's own security channel produced no patch for the affected stable releases. Following those attempts, three artifac[...]

**1. Retroactive rewriting of the project's security policy.** On 15 June 2026 — the day after the acceptance of the first XSS report on 14 June — commits [`c076737`](https://github.com/ArchiveBox[...]

**2. Retroactive modification of a prior CVE record.** On 7 July 2026 — 19 days after the researcher disclosed the same-origin XSS-to-admin-mutation chain via GHSA-h9qq-w7hq-7rxj — the CNA (GitHub[...]

**3. A prior contradictory precedent by the same maintainer.** The security policy added on 15 June 2026 states that pre-release versions do not receive CVEs. On 23 April 2026 — approximately seven [...]

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

The complete source-level audit — file paths, function references, line numbers verified against the dev-branch commit at the time of disclosure, the agent instruction template, the pseudo-terminal [...]

Withholding the technical audit until CVE publication is a deliberate disclosure choice by the researcher, not a limitation. The information sufficient for defenders to identify affected deployments ([...]

---

## Mitigation

Removal of the `archivebox/opencode/` module from any affected build closes the vulnerability. No configuration change within the module neutralizes the vector.

---

## Section 9 — Disclosure Status

This advisory is released under full disclosure following the documented failure of coordinated vulnerability disclosure with the vendor over a period of 118 days.

Coordinated disclosure was initiated on 10 June 2026 and systematically exhausted all available channels: direct vendor contact (GHSA-32m2-xhwx-92mh, accepted 14 June 2026), CISA VINCE (cases VU#643521 and VU#934895), and the MITRE CVE Program (CAN-2026-2036559, published 7 September 2026 with CVSS 4.0 base score 10.0). Escalation to the Council of Roots and CVE Board was submitted on 29 September 2026 with no response received.

During the coordination period, the vendor demonstrated a pattern of systematic obstruction: retroactive rewriting of the security policy to exclude reported vulnerability classes (15 June 2026), retroactive modification of the prior CVE-2023-45815 record (7 July 2026), and refusal to remediate across all reported findings. No patch has been provided.

Public advisory issued 13 September 2026. Forensic report finalised 5 October 2026 on the latest available release HEAD ccaa6308 (v0.9.72rc97, abx-plugins[opencode]==1.13.147), confirming code invariance despite two complete architectural refactorings since initial analysis.

Vulnerability Discovery & Lead Security Researcher: Ing. Zampier Zago
Organisation & Department: SESSION1 Department of Cyber Security, PS 1978 Limited, London, United Kingdom
Official Contact: info@ps1978ltd.it | GitHub: FUNFACTOR1
Advisory Release Date: October 5, 2026

---

## Credits

**Finder:** Zago Zampier (Zampier Zago) — https://github.com/FUNFACTOR1
Section 1, Department of Cyber Security, PS 1978 Limited
