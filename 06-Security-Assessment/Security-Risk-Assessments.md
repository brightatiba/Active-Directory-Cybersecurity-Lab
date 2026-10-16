# BlueWave Manufacturing — Security Risk Assessment

## 1. Executive Summary

A security risk assessment was conducted within the fictional BlueWave Manufacturing cybersecurity lab to evaluate identity, authentication, network segmentation and backup infrastructure risks.

The assessment used verified technical findings and remediation evidence collected during Active Directory security reviews, Group Policy hardening and pfSense firewall testing.

Four material risk scenarios were assessed using a qualitative 5 × 5 likelihood-and-impact methodology.

Technical remediation reduced the assessed exposure across all four scenarios. However, residual risks remain, particularly regarding authentication threats, cross-zone security assurance and backup recovery capabilities.

**Assessment status:** Completed for lab scope  
**Risk ratings:** Approved for case-study use; provisional pending real-world risk-owner review

## 2. Scope

The assessment covered:

- Active Directory privileged access
- Domain authentication policies
- Workstation security controls
- Manufacturing/OT network segmentation
- Backup-zone network security
- Technical control validation and residual security gaps

The assessment did not constitute a complete production security audit or ISO/IEC 27001 certification assessment.

## 3. Risk Assessment Methodology

Risks were assessed using:

**Risk Score = Likelihood × Impact**

Both likelihood and impact use a scale from 1 to 5.

### Likelihood Criteria

| Score | Rating | Description |
|---|---|---|
| 1 | Rare | Unlikely under the assessed conditions |
| 2 | Unlikely | Possible under unusual conditions |
| 3 | Possible | Credible scenario with realistic prerequisites |
| 4 | Likely | Exposure and threat conditions make occurrence plausible |
| 5 | Almost Certain | Expected frequently or exposure is exceptionally high |

### Impact Criteria

| Score | Rating | Description |
|---|---|---|
| 1 | Insignificant | Negligible business impact |
| 2 | Minor | Limited disruption and straightforward recovery |
| 3 | Moderate | Departmental disruption or meaningful remediation costs |
| 4 | Major | Significant disruption, sensitive data exposure or substantial recovery effort |
| 5 | Severe | Prolonged manufacturing outage, major recovery failure or organisation-wide compromise |

### Risk Rating Thresholds

| Score | Rating |
|---|---|
| 1–4 | Low |
| 5–9 | Medium |
| 10–16 | High |
| 17–25 | Critical |

### Assessment Assumptions

- BlueWave Manufacturing is a fictional Australian manufacturing organisation.
- Likelihood and impact scores represent qualitative consultant judgements, not measured probabilities.
- Inherent risk reflects exposure before the selected mitigating controls.
- Residual risk reflects the assessed exposure after implemented controls.
- Technical validation results support control conclusions only within the tested scope.
- Residual ratings remain provisional because not all dependent controls or business recovery arrangements were assessed.
- No formal business risk-owner acceptance is claimed.

## 4. Risk Assessment Results

| Risk ID | Risk Scenario | Inherent Score | Inherent Rating | Residual Score | Residual Rating |
|---|---|---|---|---|---|
| R-01 | Privileged AD account compromise | 3 × 5 = 15 | High | 2 × 4 = 8 | Medium |
| R-02 | Weak authentication controls | 4 × 4 = 16 | High | 3 × 4 = 12 | High |
| R-03 | Corporate/OT lateral movement | 4 × 5 = 20 | Critical | 2 × 5 = 10 | High |
| R-04 | Backup infrastructure compromise | 4 × 5 = 20 | Critical | 2 × 5 = 10 | High |

## 5. Risk Treatment Assessment

### R-01 — Privileged AD Account Compromise

**Finding:** The `admin-lwalker` administrative identity had standing Domain Admin membership.

**Business risk:** Compromise of this identity could enable domain-wide administrative control, affecting identity services and connected business systems.

**Treatment implemented:** Removed unnecessary Domain Admin membership while retaining a separate administrative identity.

**Validation:** Before-and-after privileged group membership reviewed.

**Residual assessment:** Medium (8). Remaining administrative permissions and additional privileged access protections require review.

### R-02 — Weak Authentication Controls

**Findings:** Account lockout was disabled, and the domain minimum password length was seven characters.

**Business risk:** Weak authentication controls could increase exposure to password-based compromise and unauthorised access.

**Treatment implemented:** Configured a 10-attempt lockout threshold, 15-minute lockout and reset periods, and a 14-character minimum password length.

**Validation:** Effective domain policy settings verified using PowerShell.

**Residual assessment:** High (12). Credential theft, phishing, MFA coverage and other authentication threats remain outside the completed validation scope.

### R-03 — Corporate/OT Lateral Movement

**Findings:** OT01 could connect to Corporate AD services, while CLIENT01 could initiate connections to OT01.

**Business risk:** Compromise of a corporate or OT system could enable unauthorised movement between security zones, potentially disrupting manufacturing operations.

**Treatment implemented:** Disabled unrestricted OT access, introduced explicit DNS/NTP exceptions and blocked Corporate-to-OT IPv4 traffic.

**Validation:** Before-and-after connectivity tests, pfSense blocked-traffic logs and successful approved service tests.

**Residual assessment:** High (10). IPv6 segmentation, operational management paths and patching arrangements require further assessment.

### R-04 — Backup Infrastructure Compromise

**Findings:** BACKUP01 could access Corporate/OT systems, and CLIENT01 could initiate SSH connections to BACKUP01.

**Business risk:** Compromise of backup infrastructure could affect recovery capability and increase the consequences of ransomware or destructive attacks.

**Treatment implemented:** Introduced default-deny restrictions, explicit DNS/NTP exceptions and Corporate-to-Backup IPv4 blocking.

**Validation:** Before-and-after connectivity tests, pfSense logs and successful DNS/NTP validation.

**Residual assessment:** High (10). Backup restoration, recovery objectives and approved backup-transfer mechanisms were not validated.

## 6. Security Control Effectiveness Review

| Control | Validation | Assessment |
|---|---|---|
| Privileged account separation | Account/group membership review | Effective for tested identities |
| Least-privilege membership remediation | Domain Admins before/after comparison | Remediation verified |
| Domain account policy | Effective policy queried | Configuration verified |
| Microsoft Defender Antivirus | Defender status checked | Operational on CLIENT01 |
| Windows Firewall | Effective firewall profiles checked | Configuration verified |
| Advanced auditing | Audit settings and events inspected | Observed logging controls operational |
| OT segmentation | Firewall logs and connectivity testing | Effective for tested IPv4 paths |
| Backup segmentation | Firewall logs and connectivity testing | Effective for tested IPv4 paths |
| Corporate segmentation | SSH/ICMP testing and firewall logs | Effective for tested IPv4 paths |
| Restricted-zone DNS/NTP | DNS and chrony validation | Approved services operational |

Control conclusions are limited to the systems, configurations and tests described.

## 7. Outstanding Assessment and Treatment Actions

| Action ID | Required Action | Related Risk | Priority |
|---|---|---|---|
| GAP-01 | Assess and validate IPv6 segmentation | R-03, R-04 | High |
| GAP-02 | Define authorised backup and administrative traffic | R-03, R-04 | High |
| GAP-03 | Assess MFA and privileged identity protections | R-01, R-02 | High |
| GAP-04 | Validate backup restoration and recovery processes | R-04 | High |
| GAP-05 | Establish an approved OT patch/update mechanism | R-03 | Medium |

Priority ratings are proposed for the fictional case study. Treatment owners, due dates and formal acceptance decisions would need to be assigned through organisational governance processes.

## 8. Evidence References

Supporting implementation and validation evidence is maintained in:

- `04-Active-Directory/AD-Security-Review.md`
- `05-Security-Controls/GPO-Security-Hardening.md`
- `05-Security-Controls/Network-Segmentation.md`
- `Evidence/Active-Directory/`
- `Evidence/GPO/`
- `Evidence/Network/`

These artifacts provide traceability between identified findings, implemented controls and validation outcomes.

## 9. Management Recommendations

The recommended next actions are to:

1. Prioritise residual High risks for risk-owner review.
2. Evaluate the unassessed IPv6 and privileged-access exposure.
3. Define documented requirements for backup and administrative access.
4. Assess backup recoverability rather than relying exclusively on network isolation.
5. Establish periodic control reviews and evidence retention procedures.

The proposed residual risk ratings should be reviewed whenever relevant systems, threats or security controls materially change.

## 10. Conclusion

The BlueWave Manufacturing lab assessment identified and addressed significant weaknesses in privileged access, authentication configuration and network segmentation.

Evidence demonstrated that the selected technical controls were implemented and operating as expected within the tested scope.

All four risk scenarios showed a reduction in assessed risk following treatment. Remaining exposure has been documented through provisional residual risk ratings and outstanding actions.

**Assessment outcome:** Completed for the defined lab scope, with residual risks requiring future business-owner review and treatment decisions.
