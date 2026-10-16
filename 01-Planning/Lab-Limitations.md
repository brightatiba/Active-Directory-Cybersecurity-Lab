# Lab Limitations and Assessment Boundaries

## Environment Constraints
- Oracle VirtualBox supports a simplified three-zone pfSense architecture rather than a fully segmented enterprise campus.
- BWCORP (`192.168.10.0/24`) combines corporate workstations, management and servers. It is **mixed trust**; the model does not claim these systems are individually segmented.
- BWOT (`192.168.20.0/24`) and BWBACKUP (`192.168.30.0/24`) are simulated zones with Ubuntu Server test endpoints, not operational manufacturing or validated backup applications.
- Some Windows systems run evaluation software; the project is a non-production lab.

## Security Assurance Limitations
| Area | Limitation | Follow-up |
|---|---|---|
| Network | Only selected IPv4 flows were tested; IPv6 segmentation was not validated | Assess effective IPv6 routing and firewall coverage |
| Privileged access | Standing Domain Admin membership was removed, but delegated administration/logon restrictions were not comprehensively tested | Review effective privilege |
| Endpoint | Defender, firewall and auditing verified on CLIENT01 | Expand endpoint sample and behavioural testing if production assurance is needed |
| Authentication | Effective domain policy verified; MFA and all authentication paths not assessed | Review MFA and other authentication protections |
| OT | DNS/NTP exceptions tested; maintenance/update paths not designed | Define approved maintenance traffic |
| Backup | Network isolation tested; no backup transfer, immutability or restore demonstration | Implement and test recovery |
| Compliance | Selected control-to-framework mapping only | Do not claim ISO certification, NIST compliance or Essential Eight maturity |
| Evidence | Screenshots are stored locally pending upload | Upload images under `Evidence/` and validate image links |

## Risk and Operational Meaning
Controls were validated only against specific configurations and tests. Findings may be technically remediated within this scope while broader business risks remain provisionally rated High. The fictional case study includes **no actual business risk-owner approval or acceptance**.

The [risk register](../07-GRC/Risk-Register.md) records proposed owners and outstanding actions. This document is a transparent scope boundary, not a claim of complete risk treatment.
