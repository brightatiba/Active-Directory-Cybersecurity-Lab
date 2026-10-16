# BlueWave Manufacturing — Cybersecurity Risk Register

## 1. Purpose

This risk register provides a consolidated view of the key cybersecurity risks identified during the BlueWave Manufacturing cybersecurity lab assessment.

It records the assessed risk exposure, implemented security treatments, residual risks, proposed accountability and outstanding management actions.

The register supports cybersecurity governance, risk treatment tracking and evidence-based decision-making.

**Organisation:** BlueWave Manufacturing Pty Ltd (fictional)  
**Assessment scope:** Active Directory, authentication, network segmentation and backup infrastructure  
**Risk methodology:** 5 × 5 Likelihood × Impact  
**Register status:** Case-study baseline completed; risk-owner endorsement pending

## 2. Risk Rating Methodology

Risk scores are calculated using:

**Risk Score = Likelihood × Impact**

| Score | Rating | Treatment Priority |
|---|---|---|
| 1–4 | Low | Monitor |
| 5–9 | Medium | Plan and track improvements |
| 10–16 | High | Prioritise treatment and management review |
| 17–25 | Critical | Urgent escalation and treatment decision |

Inherent risk represents exposure before the assessed mitigating controls. Residual risk represents exposure remaining after implemented controls.

All scores are qualitative case-study assessments and remain provisional pending formal business risk-owner review.

## 3. Cybersecurity Risk Register

| Risk ID | Risk Description | Inherent Risk | Residual Risk | Proposed Risk Owner | Proposed Treatment Owner | Status |
|---|---|---|---|---|---|---|
| R-01 | Compromise or misuse of privileged AD accounts could lead to unauthorised administrative access | 15 — High | 8 — Medium | IT Manager | IT Administrator | Partially treated |
| R-02 | Weak or compromised authentication credentials could enable unauthorised access to business systems | 16 — High | 12 — High | IT Manager | IT Administrator | Partially treated |
| R-03 | Unauthorised movement between Corporate and Manufacturing/OT networks could disrupt operations | 20 — Critical | 10 — High | Operations Manager | IT Manager | Partially treated |
| R-04 | Compromise or loss of backup infrastructure could undermine business recovery capabilities | 20 — Critical | 10 — High | IT Manager | IT Administrator | Partially treated |

**Treatment status note:** The identified technical findings have been remediated within their tested scope. The broader business risks remain partially treated because additional safeguards or assurance activities are outstanding.

## 4. Risk Treatment and Outstanding Actions

### R-01 — Privileged Active Directory Account Compromise

**Original finding:** `admin-lwalker` had unnecessary standing Domain Admin membership.

**Treatment completed:**
- Reviewed privileged group membership.
- Removed `admin-lwalker` from Domain Admins.
- Verified the updated membership.
- Maintained separate standard and administrative identities.

**Outstanding actions:**
- Validate delegated administrative permissions.
- Assess privileged workstation logon restrictions.
- Review additional privileged identity protections.

**Residual risk:** Medium (8) — Provisional.

**Evidence:** `04-Active-Directory/AD-Security-Review.md`

### R-02 — Weak Authentication Controls

**Original findings:** Account lockout was disabled and minimum password length was seven characters.

**Treatment completed:**
- Set account lockout threshold to 10 attempts.
- Configured a 15-minute lockout duration and reset period.
- Increased minimum password length to 14 characters.
- Verified the effective domain account policy.

**Outstanding actions:**
- Assess MFA requirements and coverage.
- Review privileged authentication safeguards.
- Establish periodic authentication-policy reviews.

**Residual risk:** High (12) — Provisional.

**Evidence:** `05-Security-Controls/GPO-Security-Hardening.md`

### R-03 — Corporate/OT Lateral Movement

**Original findings:** Excessive bidirectional connectivity existed between Corporate and Manufacturing/OT systems.

**Treatment completed:**
- Disabled unrestricted BWOT outbound access.
- Blocked unauthorised Corporate-to-OT IPv4 traffic.
- Implemented explicit DNS and NTP exceptions.
- Verified blocked connections and permitted services.

**Outstanding actions:**
- Assess IPv6 segmentation.
- Define approved OT management connectivity.
- Establish an authorised OT patch/update process.

**Residual risk:** High (10) — Provisional.

**Evidence:** `05-Security-Controls/Network-Segmentation.md`

### R-04 — Backup Infrastructure Compromise

**Original findings:** Backup systems could initiate unnecessary connections to Corporate/OT, while Corporate workstations could reach BACKUP01.

**Treatment completed:**
- Disabled unrestricted BWBACKUP outbound access.
- Restricted unauthorised Corporate-to-Backup IPv4 traffic.
- Implemented explicit DNS and NTP exceptions.
- Verified blocked connections and operational infrastructure services.

**Outstanding actions:**
- Define authorised backup-transfer and administration paths.
- Assess IPv6 exposure.
- Implement and test backup restoration.
- Review backup data protection and recovery requirements.

**Residual risk:** High (10) — Provisional.

**Evidence:** `05-Security-Controls/Network-Segmentation.md`

## 5. Treatment Action Tracker

| Action ID | Related Risk | Required Action | Proposed Owner | Priority | Status |
|---|---|---|---|---|---|
| ACT-01 | R-01 | Validate delegated access and privileged logon restrictions | IT Administrator | Medium | Open |
| ACT-02 | R-01, R-02 | Assess MFA and privileged authentication safeguards | IT Manager | High | Open |
| ACT-03 | R-03, R-04 | Assess IPv6 segmentation | IT Manager | High | Open |
| ACT-04 | R-03, R-04 | Define authorised management and backup-transfer access | IT Manager | High | Open |
| ACT-05 | R-04 | Implement and validate backup restoration | IT Administrator | High | Open |
| ACT-06 | R-03 | Establish an approved OT patch/update mechanism | IT Manager | Medium | Open |

Target completion dates are not assigned because no operational project schedule or owner commitments have been established.

## 6. Risk Acceptance and Governance

The risk owners listed in this register are proposed fictional business roles.

| Governance Item | Current Status |
|---|---|
| Risk identification | Completed |
| Risk scoring | Approved for case-study use |
| Technical remediation | Completed for identified, tested findings |
| Residual risk assessment | Provisional |
| Proposed ownership | Agreed for case study |
| Business risk-owner endorsement | Pending |
| Formal residual risk acceptance | Not obtained |
| Outstanding treatment actions | Open |

No residual risk is represented as formally accepted.

In an operational environment, risk owners would review the assessment, confirm business impacts, authorise treatment priorities and approve acceptance decisions where permitted by the organisation's risk appetite.

## 7. Risk Monitoring and Review

The proposed governance process is to review this register:

- Quarterly as part of cybersecurity risk reporting.
- Following major infrastructure or access-control changes.
- After significant security incidents.
- When remediation activities are completed.
- When newly identified threats materially change the risk exposure.

Risk scores should be reassessed when controls, exposure or business impacts change.

## 8. Supporting Evidence

| Artifact | Purpose |
|---|---|
| `04-Active-Directory/AD-Security-Review.md` | Privileged-access finding and remediation |
| `05-Security-Controls/GPO-Security-Hardening.md` | Authentication and endpoint security controls |
| `05-Security-Controls/Network-Segmentation.md` | Network risk treatment and validation |
| `06-Security-Assessment/Security-Risk-Assessments.md` | Detailed risk methodology, scoring and rationale |
| `07-GRC/Control-Mapping.md` | ISO 27001, NIST CSF 2.0 and Essential Eight mapping |
| `Evidence/` | Supporting technical screenshots and test results |

## 9. Management Summary

The assessment identified four material cybersecurity risk scenarios.

Technical remediation reduced the assessed risk exposure across privileged access, authentication security and network segmentation.

Following treatment:

- One risk is provisionally rated Medium.
- Three risks remain provisionally rated High.
- No risks have been formally accepted by business risk owners.
- Additional treatment and validation activities remain outstanding.

**Recommended management priority:** Review the three High residual risks, establish treatment responsibilities and target dates, and validate backup recovery arrangements and remaining access-control gaps.

## 10. Conclusion

The BlueWave cybersecurity risk register demonstrates the identification, assessment, treatment and ongoing governance of cybersecurity risks.

It provides traceability between risk scenarios, implemented controls, residual exposure, proposed ownership, outstanding actions and technical evidence.

**Register status:** Completed for the fictional case study, with provisional residual ratings and outstanding governance decisions.
