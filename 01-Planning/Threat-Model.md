# Focused Threat Model

## Scope and Assets
This threat model supports the selected BlueWave lab assessment, rather than a comprehensive enterprise threat-modelling exercise.

| Asset / Trust Zone | Security Interest |
|---|---|
| DC01 / `bluewave.local` | Domain authentication, DNS, administrative privilege |
| CLIENT01 and ADMIN01 | Standard and privileged activity |
| FILESERVER01 | Corporate file service and information access |
| BWOT / OT01 | Simulated operational network boundary |
| BWBACKUP / BACKUP01 | Recovery infrastructure boundary |
| PFSENSE01 | Security-zone routing, filtering and logs |

## Threat Scenarios

| Risk | Threat Scenario | Initial Condition Observed | Control / Evidence |
|---|---|---|---|
| R-01 | Credential compromise leads to excessive domain privilege | `admin-lwalker` had standing Domain Admin membership | Membership removed; AD group comparison |
| R-02 | Password guessing or compromised credentials permit unauthorised access | Account lockout disabled; 7-character minimum password length | Effective domain policy strengthened and queried |
| R-03 | Lateral movement between Corporate and Manufacturing/OT | OT01 reached DC01 DNS/LDAP; CLIENT01 reached OT01 SSH | Restrictive pfSense IPv4 rules, blocked tests/logs, explicit DNS/NTP |
| R-04 | Backup infrastructure exposure increases recovery disruption risk | BACKUP01 initiated Corporate/OT traffic; CLIENT01 reached BACKUP01 SSH | Restrictive pfSense IPv4 rules, blocked tests/logs, explicit DNS/NTP |

## Trust Boundaries
BWCORP: `192.168.10.0/24`; BWOT: `192.168.20.0/24`; BWBACKUP: `192.168.30.0/24`. The Corporate/Management zone combines users, servers and administration because of lab constraints, and therefore represents mixed trust. pfSense separates the zones, but rules must be validated in both directions. An OU is a policy/administration scope, **not a security boundary**.

## Key Assumptions and Limitations
This is a fictional manufacturer; threat probability and financial consequences are qualitative assumptions. The tested IPv4 paths were remediated, not every possible network path. IPv6 isolation, MFA, approved administrative/backup transfer flows, OT patch access, data recovery and broader endpoint hardening require additional assessment. A failed ping alone is not proof of a firewall control; pfSense logging was used where available.

## Risk Treatment and Traceability
The [security risk assessment](../06-Security-Assessment/Security-Risk-Assessments.md) contains the approved **case-study**, provisional inherent/residual scores. The [network segmentation record](../05-Security-Controls/Network-Segmentation.md) and [AD security review](../04-Active-Directory/AD-Security-Review.md) describe observed findings, corrective action and validation. No formal business risk acceptance is claimed.
