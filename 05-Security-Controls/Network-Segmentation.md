# Network Segmentation — Security Control Implementation

## 1. Overview

A network segmentation security assessment and remediation exercise was conducted within the BlueWave Manufacturing cybersecurity lab using pfSense.

The objective was to identify excessive communication between Corporate, Manufacturing/OT and Backup security zones, implement least-privilege firewall controls, and validate their effectiveness using before-and-after connectivity tests and firewall logs.

**Status:** Technical implementation and validation completed.

## 2. Network Architecture

| Security Zone | Network | Gateway | Systems |
|---|---|---|---|
| BWCORP | `192.168.10.0/24` | `192.168.10.1` | DC01, FILESERVER01, CLIENT01, ADMIN01 |
| BWOT | `192.168.20.0/24` | `192.168.20.1` | OT01 |
| BWBACKUP | `192.168.30.0/24` | `192.168.30.1` | BACKUP01 |

pfSense provides routing and firewall enforcement between these zones.

## 3. Initial Security Findings

The baseline configuration contained broad firewall rules permitting traffic from each internal network to any destination.

| Finding | Security Risk |
|---|---|
| NET-01: Excessive OT-to-Corporate access | Increased exposure of Active Directory infrastructure to potentially compromised OT systems |
| NET-02: Excessive Backup-to-Corporate/OT access | Unnecessary communication paths between recovery infrastructure and other trust zones |
| NET-03: Excessive Corporate-to-restricted-zone access | Increased potential for lateral movement from compromised corporate workstations |

Baseline tests confirmed that OT01 and BACKUP01 could reach DC01 on TCP/53 and TCP/389, while CLIENT01 could initiate SSH connections to OT01 and BACKUP01.

## 4. Security Control Implementation

### Manufacturing/OT Zone

The unrestricted `BWOT subnets → any` rule was disabled.

Two narrowly scoped service exceptions were configured:

| Source | Destination | Protocol/Port | Action |
|---|---|---|---|
| OT01 | pfSense BWOT address | TCP/UDP 53 | Allow |
| OT01 | pfSense BWOT address | UDP 123 | Allow |
| OT01 | Other unmatched destinations | Any | Default deny |

DNS resolution and NTP synchronisation were validated.

Direct access from OT01 to DC01 on TCP/53 and TCP/389 remained blocked.

### Backup Zone

The unrestricted `BWBACKUP subnets → any` rule was disabled.

The following service exceptions were implemented:

| Source | Destination | Protocol/Port | Action |
|---|---|---|---|
| BACKUP01 | pfSense BWBACKUP address | TCP/UDP 53 | Allow |
| BACKUP01 | pfSense BWBACKUP address | UDP 123 | Allow |
| BACKUP01 | Other unmatched destinations | Any | Default deny |

DNS resolution and synchronisation to pfSense NTP were validated.

Direct access from BACKUP01 to DC01 on TCP/53 and TCP/389 remained blocked.

### Corporate Zone

Two explicit block rules were added above the existing broad IPv4 LAN allow rule.

| Source | Destination | Protocol | Action |
|---|---|---|---|
| BWCORP subnet | `192.168.20.0/24` | IPv4 Any | Block |
| BWCORP subnet | `192.168.30.0/24` | IPv4 Any | Block |

The rules were logged to support enforcement verification.

Existing Corporate connectivity to domain services was preserved.

## 5. Before-and-After Control Validation

| Test | Before | After |
|---|---|---|
| OT01 → DC01 TCP/53 | Allowed | Blocked |
| OT01 → DC01 TCP/389 | Allowed | Blocked |
| BACKUP01 → DC01 TCP/53 | Allowed | Blocked |
| BACKUP01 → DC01 TCP/389 | Allowed | Blocked |
| BACKUP01 → OT01 ICMP | Allowed | Blocked |
| CLIENT01 → OT01 TCP/22 | Allowed | Blocked |
| CLIENT01 → BACKUP01 TCP/22 | Allowed | Blocked |
| CLIENT01 → OT01 ICMP | Allowed | Blocked |
| CLIENT01 → BACKUP01 ICMP | Allowed | Blocked |
| OT01 → pfSense DNS/NTP | Not available under initial default-deny policy | Operational after explicit exceptions |
| BACKUP01 → pfSense DNS/NTP | Not available under initial default-deny policy | Operational after explicit exceptions |

The post-remediation tests demonstrated that the selected unauthorised connection paths were restricted while required infrastructure services remained operational.

## 6. Firewall Log Validation

pfSense firewall logs were reviewed to identify blocked connections.

Examples included:

- OT01 attempting TCP/53 and TCP/389 connections to DC01
- BACKUP01 attempting connections to Corporate and OT destinations
- CLIENT01 attempting ICMP connections to OT01 and BACKUP01

Relevant blocked entries identified the firewall interfaces, destination addresses and enforcement rules.

## 7. Operational Continuity Validation

The following checks confirmed that selected legitimate services remained available:

- OT01 DNS resolution through pfSense
- OT01 NTP synchronisation with `192.168.20.1`
- BACKUP01 DNS resolution through pfSense
- BACKUP01 NTP synchronisation with `192.168.30.1`
- CLIENT01 Active Directory secure channel returned `True`
- CLIENT01 successfully resolved `dc01.bluewave.local` to `192.168.10.10`

## 8. Residual Limitations

The following limitations remain:

- IPv6 network segmentation was not assessed or validated.
- Approved Corporate-to-OT administration flows have not been implemented.
- An operational backup-transfer workflow has not been deployed or validated.
- OT software updates require an approved maintenance or update-access mechanism.
- Additional rule review would be required before using the configuration in a production environment.

These items will be considered during the consolidated GRC risk assessment.

## 9. Evidence

The following committed screenshots provide traceability for the network-control findings and validation:

| Evidence | Screenshot |
|---|---|
| Initial unrestricted BWCORP rules | [Corporate baseline](../Evidence/Network/BWCORP-rules-before.png) |
| Initial unrestricted BWOT rules | [OT baseline](../Evidence/Network/BWOT-rules-before.png) |
| Initial unrestricted BWBACKUP rules | [Backup baseline](../Evidence/Network/BWBACKUP-rules-before.png) |
| Corporate-to-restricted-zone firewall controls | [Corporate block rules](../Evidence/Network/BWCORP-block-rule.png) |
| Corporate blocked-traffic log | [Corporate block log](../Evidence/Network/BWCORP-firewall-block-rule.png) |
| OT blocked-traffic evidence | [OT block evidence](../Evidence/Network/BWOT-block-rule.png) |
| Backup blocked-traffic evidence | [Backup block evidence](../Evidence/Network/BWBACKUP-block-rule.png) |

DNS and NTP continuity is documented by the test results in Section 7; separate DNS/NTP screenshots are not currently committed. These screenshots support selected IPv4 path tests, not complete network isolation.

## 10. GRC Outcome

This exercise demonstrates:

- Network security risk identification
- Least-privilege firewall design
- Technical security control implementation
- Testing of preventive controls
- Evidence-based remediation validation
- Consideration of operational availability
- Documentation of residual risk and limitations

The results provide supporting evidence for applicable ISO/IEC 27001, NIST CSF and ASD Essential Eight control assessments.

## 11. Conclusion

The tested excessive inter-zone connectivity paths were successfully remediated through pfSense firewall restrictions.

Essential DNS and NTP services remained available to the restricted zones, while Corporate domain functionality was preserved.

**Assessment outcome:** The selected IPv4 segmentation controls were implemented and validated successfully, with documented residual limitations.
