# BlueWave Manufacturing — Security Control Mapping

## 1. Overview

This document maps security controls implemented and assessed in the BlueWave Manufacturing cybersecurity lab to relevant requirements and outcomes from:

- ISO/IEC 27001:2022 Annex A
- NIST Cybersecurity Framework (CSF) 2.0
- ASD Essential Eight Maturity Model

The objective is to demonstrate traceability between identified cybersecurity risks, implemented safeguards, technical validation evidence and recognised security frameworks.

**Assessment scope:** BlueWave Active Directory Cybersecurity Lab  
**Mapping status:** Completed for selected lab controls  
**Compliance status:** Partial control alignment only; no certification or Essential Eight maturity level claimed

## 2. Assessment Approach

Each control was mapped using the following sequence:

**Risk → Control objective → Implementation → Validation evidence → Framework reference → Remaining gap**

Assessment classifications:

| Classification | Meaning |
|---|---|
| Validated | Selected technical configuration or behaviour was verified |
| Partially validated | Some relevant requirements or behaviours were tested, but important coverage remains |
| Gap | Required implementation or validation evidence is unavailable |
| Not assessed | Outside the selected lab assessment scope |

A validated lab control does not automatically establish full compliance with the corresponding framework requirement.

## 3. Security Control Mapping Matrix

| Control ID | Security Control | Related Risk | ISO/IEC 27001:2022 Annex A | NIST CSF 2.0 | Assessment |
|---|---|---|---|---|---|
| IAM-01 | Separate standard and administrative accounts | R-01 | A.5.15 Access control; A.8.2 Privileged access rights | PR.AA-01; PR.AA-05 | Validated for selected identities |
| IAM-02 | Remove unnecessary Domain Admin membership | R-01 | A.8.2 Privileged access rights | PR.AA-05 | Specific membership remediation validated |
| AUTH-01 | Domain password and account lockout policy | R-02 | A.5.17 Authentication information; A.8.5 Secure authentication | PR.AA-03; PR.PS-01 | Configuration validated |
| END-01 | Microsoft Defender Antivirus | R-02, R-03 | A.8.7 Protection against malware | PR.PS-01 | Operational status validated on CLIENT01 |
| END-02 | Windows Defender Firewall baseline | R-03, R-04 | A.8.9 Configuration management; A.8.20 Networks security | PR.PS-01; PR.IR-01 | Configuration validated |
| LOG-01 | Windows advanced security auditing | R-01, R-02 | A.8.15 Logging; A.8.16 Monitoring activities | PR.PS-04; DE.CM-03 | Selected event generation validated |
| NET-01 | OT network isolation | R-03 | A.8.20 Networks security; A.8.22 Segregation of networks | PR.IR-01 | Tested IPv4 paths validated |
| NET-02 | Backup network isolation | R-04 | A.8.20 Networks security; A.8.22 Segregation of networks | PR.IR-01 | Tested IPv4 paths validated |
| NET-03 | Corporate-to-restricted-zone firewall rules | R-03, R-04 | A.8.20 Networks security; A.8.22 Segregation of networks | PR.IR-01 | Tested IPv4 paths validated |
| NET-04 | Restricted-zone DNS/NTP exceptions | R-03, R-04 | A.8.20 Networks security; A.8.22 Segregation of networks | PR.IR-01 | Approved lab service paths validated |

Framework references represent relevant control or outcome alignment, not proof that the entire requirement has been satisfied.

## 4. ASD Essential Eight Alignment

The Essential Eight is assessed separately because its maturity model contains specific requirements that cannot be satisfied through general security hardening alone.

| Essential Eight Strategy | BlueWave Evidence | Assessment |
|---|---|---|
| Restrict administrative privileges | Separate `lwalker` and `admin-lwalker` identities; unnecessary Domain Admin membership removed | Partial implementation evidence |
| Multi-factor authentication | No MFA implementation evidence collected | Gap |
| Regular backups | Backup network isolated, but backup creation and restoration were not demonstrated | Gap |
| Patch applications | No application patch compliance assessment performed | Not assessed |
| Patch operating systems | No operating system patch compliance assessment performed | Not assessed |
| Application control | Microsoft Defender Antivirus operational, but application allowlisting was not implemented or tested | Not assessed |
| Restrict Microsoft Office macros | No macro security configuration or validation performed | Not assessed |
| User application hardening | No comprehensive browser or application hardening assessment performed | Not assessed |

### Essential Eight Assessment Conclusion

The privileged-account separation and Domain Admin remediation provide supporting evidence for the **Restrict administrative privileges** strategy.

However, effective logon restrictions, privileged-account usage controls and other maturity requirements were not comprehensively validated.

Backup-zone firewall isolation may support recovery infrastructure protection, but it does not demonstrate compliance with the **Regular backups** strategy.

Microsoft Defender Antivirus does not, by itself, meet the **Application control** strategy's requirements.

**No overall Essential Eight maturity level has been assigned.**

## 5. Risk-to-Control Traceability

| Risk ID | Residual Rating | Implemented Controls | Remaining Gap |
|---|---|---|---|
| R-01 — Privileged AD account compromise | Medium (8) | IAM-01, IAM-02, LOG-01 | Administrative delegation, privileged logon restrictions and additional credential protections require assessment |
| R-02 — Weak authentication controls | High (12) | AUTH-01, LOG-01, END-01 | MFA, broader authentication safeguards and behavioural validation remain incomplete |
| R-03 — Corporate/OT lateral movement | High (10) | NET-01, NET-03, NET-04, END-02 | IPv6 segmentation, approved OT management paths and patching arrangements require assessment |
| R-04 — Backup infrastructure compromise | High (10) | NET-02, NET-03, NET-04, END-02 | Recovery testing, backup data protection, IPv6 and authorised transfer paths require assessment |

Residual ratings are qualitative assessments approved for fictional case-study use and remain provisional pending business risk-owner review.

## 6. Evidence Register

| Evidence Source | Control Coverage |
|---|---|
| `04-Active-Directory/AD-Implementation.md` | IAM-01 |
| `04-Active-Directory/AD-Security-Review.md` | IAM-01, IAM-02 |
| `05-Security-Controls/GPO-Security-Hardening.md` | AUTH-01, END-01, END-02, LOG-01 |
| `05-Security-Controls/Network-Segmentation.md` | NET-01, NET-02, NET-03, NET-04 |
| `06-Security-Assessment/Security-Risk-Assessments.md` | Risk ratings, treatment decisions and residual gaps |
| `Evidence/Active-Directory/` | Privileged membership before/after screenshots |
| `Evidence/GPO/` | Effective security settings, GPO application and audit results |
| `Evidence/Network/` | Firewall configurations, connectivity tests, block logs and DNS/NTP verification |

Evidence demonstrates the selected lab tests and should not be interpreted as comprehensive production assurance.

## 7. Outstanding GRC Actions

| Gap ID | Recommended Action | Priority |
|---|---|---|
| GAP-01 | Assess and test IPv6 isolation | High |
| GAP-02 | Define authorised OT/Backup management and data-transfer flows | High |
| GAP-03 | Assess MFA and privileged account protections | High |
| GAP-04 | Implement and validate backup and restore procedures | High |
| GAP-05 | Establish an approved OT patch/update mechanism | Medium |

These represent future assessment or treatment activities. They are not presented as completed controls or formally accepted risks.

## 8. Framework Reference Sources

- ISO/IEC 27001:2022 — Information security management systems: https://www.iso.org/standard/27001
- NIST Cybersecurity Framework 2.0: https://doi.org/10.6028/NIST.CSWP.29
- ASD Essential Eight Maturity Model: https://www.cyber.gov.au/publication/essential-eight-maturity-model
- ASD Essential Eight / ISM Mapping: https://www.cyber.gov.au/resources-business-and-government/essential-cyber-security/essential-eight/essential-eight-maturity-model-ism-mapping

## 9. Conclusion

The BlueWave Manufacturing lab demonstrates how selected technical security controls can be implemented, validated and mapped to recognised cybersecurity risk management frameworks.

The assessment provides traceability from four identified risk scenarios through ten selected control activities to technical evidence and framework references.

The results support selected ISO/IEC 27001 Annex A controls and NIST CSF 2.0 outcomes, with limited supporting evidence for the ASD Essential Eight strategy concerning administrative privileges.

Remaining requirements, unassessed control areas and residual risks are explicitly documented.

**Outcome:** Evidence-based control mapping completed for the defined lab scope. Full framework compliance and Essential Eight maturity have not been established.
