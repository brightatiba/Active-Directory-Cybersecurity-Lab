# Restricted Zone Test Systems

## Overview

Two lightweight Linux systems were deployed in the restricted network zones to support firewall, segmentation and security-control validation.

| System | Security Zone | Address | Gateway |
|---|---|---|---|
| OT01 | Manufacturing / OT | `192.168.20.10/24` | `192.168.20.1` |
| BACKUP01 | Backup | `192.168.30.10/24` | `192.168.30.1` |

Both systems use static addressing to provide predictable targets for firewall rules, logging and security testing.

---

## OT01

OT01 represents a system within the simulated Manufacturing / OT environment.

The system is intentionally not joined to the Corporate Active Directory domain.

Configuration:

- Ubuntu Server
- Static IPv4: `192.168.20.10/24`
- Gateway: `192.168.20.1`
- Security Zone: BWOT

Connectivity to the pfSense BWOT gateway was successfully validated.

---

## BACKUP01

BACKUP01 represents protected recovery infrastructure within the dedicated Backup security zone.

Configuration:

- Ubuntu Server
- Static IPv4: `192.168.30.10/24`
- Gateway: `192.168.30.1`
- Security Zone: BWBACKUP

Connectivity to the pfSense BWBACKUP gateway was successfully validated.

---

## Baseline Segmentation Testing

Before firewall hardening, connectivity testing was performed to identify excessive inter-zone access.

### OT Baseline

OT01 was able to:

- reach the Corporate pfSense interface;
- reach DC01;
- connect to DC01 on TCP/53; and
- connect to DC01 on TCP/389.

This demonstrated that the initial `BWOT net → any` firewall rule allowed unnecessary OT-to-Corporate communication.

### Backup Baseline

BACKUP01 was able to:

- reach the Corporate pfSense interface;
- reach DC01;
- reach OT01;
- connect to DC01 on TCP/53; and
- connect to DC01 on TCP/389.

This demonstrated that the initial `BWBACKUP net → any` rule did not adequately isolate recovery infrastructure from other security zones.

FILESERVER01 did not respond to ICMP testing from either restricted zone. This result alone was not treated as evidence of pfSense blocking because host-based firewall controls may also affect ICMP responses.

---

## Security Finding

**Finding:** Internal firewall rules currently permit excessive communication between Corporate, OT and Backup security zones.

**Risk:** Compromise of a system in one zone could provide unnecessary network paths toward systems in other trust zones, increasing opportunities for reconnaissance and lateral movement.

**Planned Remediation:** Replace broad `network → any` rules with least-privilege inter-zone policies using default-deny and explicitly authorised traffic.

The same connectivity tests will be repeated after firewall hardening to validate that prohibited communication is blocked while required business traffic remains available.

---

## Security Outcome

OT01 and BACKUP01 provide dedicated endpoints for testing network segmentation and firewall enforcement.

The baseline assessment identified excessive inter-zone connectivity that will be remediated and retested during the Network Security phase.
