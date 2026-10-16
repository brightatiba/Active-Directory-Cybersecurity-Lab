# Security Objectives

This document records the selected, measurable objectives of the BlueWave AD Security & GRC Lab. The objective is evidence-based control assessment, not complete enterprise hardening.

| ID | Security Objective | Evidence / Validation | Status |
|---|---|---|---|
| SO-01 | Establish domain identity administration and role-based organisation | AD DS, DNS, OUs, department groups, domain joins and secure-channel tests | Implemented |
| SO-02 | Separate daily and privileged identities | `lwalker` and `admin-lwalker` memberships | Validated for selected identities |
| SO-03 | Remove excessive standing Domain Admin membership | Before/after group membership review | Remediated |
| SO-04 | Strengthen the default domain authentication policy | 14-character minimum, 10-attempt lockout, 15-minute duration/window | Configuration verified |
| SO-05 | Apply workstation endpoint controls | Defender operational; firewall profiles configured; workstation GPO applied | Validated on CLIENT01 |
| SO-06 | Record useful security events | Audit policy and observed events 4624/4688 | Selected logging validated |
| SO-07 | Restrict tested IPv4 inter-zone access | pfSense rules, connection tests and blocked log entries | Tested paths remediated |
| SO-08 | Preserve justified OT/backup infrastructure services | DNS queries and chrony synchronisation through pfSense | Validated |
| SO-09 | Assess and communicate cyber risk | Four-scenario assessment, treatment tracking, residual ratings and control mapping | Completed for case study |

## Assurance Boundaries
A configuration check is not proof of full operational effectiveness. The tested firewall connections do not establish universal zone isolation. The workstation baseline was validated on CLIENT01; other systems were not given equivalent assurance. The Backup zone is segmented, but backup restoration was not tested. IPv6, MFA coverage, authorised maintenance/backup flows and full Essential Eight maturity remain out of assessed scope or open actions.

## GRC Reporting
The [risk assessment](../06-Security-Assessment/Security-Risk-Assessments.md) explains risk ratings and limitations. The [risk register](../07-GRC/Risk-Register.md) tracks provisional residual exposures, and the [control mapping](../07-GRC/Control-Mapping.md) identifies supporting ISO/IEC 27001:2022, NIST CSF 2.0 and ASD Essential Eight references without claiming certification or maturity.
