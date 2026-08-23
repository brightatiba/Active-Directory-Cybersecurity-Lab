# Active Directory Cybersecurity Lab — Security Architecture Overview

## 1. Document Purpose

This document defines the high-level cybersecurity architecture for the **Active-Directory-Cybersecurity-Lab** based on the fictional BlueWave Manufacturing Pty Ltd environment.

The architecture translates the security requirements established during Phase 01 into a technical security design.

It defines:

* security architecture principles;
* major infrastructure components;
* security zones;
* trust boundaries;
* Active Directory security architecture;
* identity and privileged-access concepts;
* network security architecture;
* infrastructure protection;
* logging and monitoring;
* backup protection;
* security-control layers;
* expected security flows; and
* the relationship between threats, controls, and architecture.

Detailed implementation specifications will be developed in later architecture and implementation phases.

The architecture follows the principle:

**Business Requirement → Security Objective → Threat → Architecture → Control → Implementation → Validation → Evidence**

---

# 2. Architecture Goals

The architecture is designed to achieve the following high-level outcomes:

1. Protect Active Directory as critical security infrastructure.
2. Reduce exposure of privileged credentials.
3. Apply least privilege to users and administrators.
4. Reduce lateral-movement opportunities.
5. Separate systems with significantly different security requirements.
6. Protect Manufacturing / OT systems from corporate compromise.
7. Protect backup infrastructure from production-network compromise.
8. Enforce network access using pfSense.
9. Harden Windows systems using centrally managed security policies.
10. Generate sufficient security logs for detection and investigation.
11. Validate security controls rather than assuming configuration equals effectiveness.
12. Preserve recovery capability following a cyber incident.

---

# 3. Security Architecture Principles

The architecture is based on the following principles.

## 3.1 Defence in Depth

No single security control is expected to prevent every attack.

Security controls are implemented across multiple layers:

```text
Physical / Virtual Infrastructure
             ↓
        Network Security
             ↓
        Host Security
             ↓
       Identity Security
             ↓
      Privileged Access
             ↓
     Application / Data
             ↓
     Logging / Monitoring
             ↓
      Backup / Recovery
```

If one control fails, additional controls should reduce the likelihood or impact of further compromise.

---

## 3.2 Least Privilege

Users, administrators, services, and systems should receive only the permissions necessary to perform authorised functions.

Least privilege applies to:

* Active Directory groups;
* administrative rights;
* file permissions;
* firewall rules;
* remote administration;
* service accounts; and
* security configuration.

---

## 3.3 Assume Breach

The architecture assumes that preventative controls may eventually fail.

For example, a corporate workstation may become compromised despite endpoint protections.

The architecture must therefore also:

* limit lateral movement;
* protect privileged credentials;
* restrict access to critical systems;
* detect suspicious activity;
* protect OT;
* protect backups; and
* preserve recovery capability.

---

## 3.4 Segmentation

Systems with significantly different security requirements should be separated where technically feasible.

The current lab implements three major security zones:

1. Corporate / Management
2. Manufacturing / OT
3. Backup

Communication between security zones is controlled through pfSense.

---

## 3.5 Secure-by-Default Configuration

Systems should not provide unnecessary access or functionality.

Where practical:

**Default Deny → Explicitly Permit Required Access**

This principle applies particularly to:

* firewall rules;
* administrative access;
* privileged group membership; and
* resource permissions.

---

## 3.6 Centralised Security Management

Where practical, security configuration should be centrally managed using technologies such as:

* Active Directory;
* Group Policy;
* pfSense; and
* PowerShell.

Centralisation reduces configuration inconsistency and supports repeatable security controls.

---

## 3.7 Validation and Evidence

A control is not considered effective solely because it has been configured.

Security controls should be validated through:

* configuration inspection;
* GPResult;
* PowerShell;
* authentication testing;
* firewall testing;
* access-control testing;
* Windows Event Logs;
* pfSense logs; and
* documented evidence.

---

# 4. High-Level Architecture

The current lab architecture is:

```text
                         HOME NETWORK
                              |
                              |
                        pfSense WAN
                              |
                     +----------------+
                     |   PFSENSE01    |
                     | Firewall /     |
                     | Security       |
                     | Gateway        |
                     +-------+--------+
                             |
          +------------------+------------------+
          |                  |                  |
          |                  |                  |
          v                  v                  v
  CORPORATE / MGMT      MANUFACTURING       BACKUP
       ZONE                / OT ZONE          ZONE
          |                  |                  |
   +------+------+          OT01             BACKUP01
   |      |      |
   |      |      |
  DC01 FILESERVER01 CLIENT01
   |
 ADMIN01
```

PFSENSE01 provides the primary network enforcement point between the security zones.

---

# 5. Core Infrastructure Components

## 5.1 PFSENSE01

### Role

Network firewall, router, and security-zone enforcement point.

### Security Functions

PFSENSE01 provides:

* inter-zone routing;
* traffic filtering;
* network segmentation;
* controlled communication;
* firewall logging;
* attack-surface reduction; and
* isolation of higher-risk security zones.

### Criticality

**Critical**

Compromise or serious misconfiguration of PFSENSE01 could weaken multiple network security boundaries.

---

# 6. DC01 — Domain Controller

## Role

DC01 provides:

* Active Directory Domain Services;
* domain authentication;
* authorisation;
* directory services;
* Group Policy infrastructure; and
* DNS.

## Security Functions

DC01 forms part of the organisation's identity security control plane.

It controls or supports:

* user authentication;
* computer authentication;
* security-group membership;
* administrative access;
* Group Policy;
* DNS-based AD discovery; and
* security policy distribution.

## Criticality

**Critical**

DC01 is one of the highest-value assets in the environment.

---

# 7. FILESERVER01

## Role

FILESERVER01 represents organisational file and resource services.

## Security Functions

The server provides a platform for implementing and validating:

* group-based authorisation;
* NTFS permissions;
* least privilege;
* departmental access;
* server hardening;
* security logging; and
* access-control testing.

## Criticality

**High**

---

# 8. CLIENT01

## Role

CLIENT01 represents a standard corporate user workstation.

## Security Functions

CLIENT01 provides a platform for testing:

* user authentication;
* Group Policy application;
* endpoint hardening;
* Windows Firewall;
* Microsoft Defender;
* access permissions;
* PowerShell security;
* event logging; and
* network restrictions.

## Threat Perspective

CLIENT01 is treated as a realistic potential initial foothold for an attacker.

The architecture therefore assumes:

```text
CLIENT01 may become compromised.
```

Security controls must limit what an attacker could do next.

---

# 9. ADMIN01

## Role

ADMIN01 represents a system used for security-sensitive administrative activity.

## Security Purpose

Separating administrative activity from normal workstation activity reduces privileged credential exposure.

ADMIN01 supports the concept:

```text
Normal User Activity
        ↓
     CLIENT01


Privileged Administration
        ↓
     ADMIN01
```

## Production Direction

A mature enterprise architecture could replace this concept with dedicated Privileged Access Workstations or equivalent hardened administrative endpoints.

---

# 10. OT01

## Role

OT01 represents BlueWave Manufacturing's industrial / Operational Technology environment.

## Security Purpose

OT01 provides a target for validating:

* network isolation;
* corporate-to-OT firewall restrictions;
* administrative access restrictions;
* attack-path containment; and
* segmentation controls.

## Criticality

**Critical**

A real OT compromise could affect production, availability, equipment, business operations, and potentially safety.

---

# 11. BACKUP01

## Role

BACKUP01 represents BlueWave's protected backup and recovery infrastructure.

## Security Purpose

BACKUP01 provides a platform for demonstrating:

* backup-zone isolation;
* restricted administrative access;
* network restrictions;
* ransomware resilience concepts; and
* recovery protection.

## Criticality

**Critical**

Recovery infrastructure must remain protected even when production systems are compromised.

---

# 12. Security Zone Architecture

## Zone 1 — Corporate / Management

Contains systems representing:

* Domain Controllers;
* servers;
* corporate endpoints;
* administrative systems;
* IT/management systems; and
* other corporate resources.

Example systems:

```text
DC01
FILESERVER01
CLIENT01
ADMIN01
```

### Security Concern

Because multiple trust levels exist inside this zone, host-based security controls become particularly important.

Controls include:

* Windows Firewall;
* Group Policy;
* least privilege;
* privileged-account separation;
* server hardening;
* endpoint hardening; and
* logging.

---

# 13. Zone 2 — Manufacturing / OT

Contains:

```text
OT01
```

and represents BlueWave's manufacturing environment.

### Security Objective

Corporate compromise must not automatically provide unrestricted access to manufacturing infrastructure.

### Primary Control

PFSENSE01.

### Desired Behaviour

```text
CLIENT01
    |
    | Unauthorised Traffic
    v
PFSENSE01
    |
    X
    |
   OT01
```

Only explicitly authorised communication should cross the Corporate-to-OT boundary.

---

# 14. Zone 3 — Backup

Contains:

```text
BACKUP01
```

and represents protected recovery infrastructure.

### Security Objective

Compromise of production systems should not automatically provide unrestricted access to backup infrastructure.

### Desired Behaviour

```text
Compromised Corporate System
           |
           | Unauthorised Traffic
           v
       PFSENSE01
           |
           X
           |
        BACKUP01
```

Required backup communication should later be explicitly documented and permitted.

---

# 15. Trust Boundaries

The architecture contains several significant trust boundaries.

| ID    | Trust Boundary                            | Enforcement                   |
| ----- | ----------------------------------------- | ----------------------------- |
| TB-01 | Home Network → Lab                        | pfSense WAN                   |
| TB-02 | Corporate → OT                            | pfSense                       |
| TB-03 | Corporate → Backup                        | pfSense                       |
| TB-04 | Standard User → Privileged Administration | Identity controls             |
| TB-05 | Workstation → Domain Controller           | AD + Windows security         |
| TB-06 | Administrator → Security Configuration    | Privileged access             |
| TB-07 | User → Protected Resource                 | Security groups + permissions |

Trust boundaries should not be crossed solely because network connectivity exists.

Authentication and authorisation requirements remain applicable.

---

# 16. Identity Security Architecture

Active Directory provides the central identity architecture.

The high-level identity model is:

```text
                ACTIVE DIRECTORY
                       |
       +---------------+---------------+
       |               |               |
       v               v               v
     Users       Security Groups    Computers
       |               |
       +-------+-------+
               |
               v
          Authorisation
               |
               v
       Organisational Resources
```

Access should be assigned through controlled group membership wherever practical.

---

# 17. Organisational Unit Architecture

Organisational Units support:

* administrative organisation;
* Group Policy targeting;
* security-policy application;
* delegated administration; and
* separation of different object types.

The existing OU structure from the BlueWave environment will be reviewed during the Active Directory security architecture phase.

The security objective is not simply to make Active Directory visually organised.

OU design should support:

**Security Policy + Delegation + Manageability**

---

# 18. Security Group Architecture

Security groups provide the primary mechanism for role-based access.

The preferred access model is:

```text
User
  ↓
Role / Department Group
  ↓
Resource Permission
  ↓
File / System / Service
```

Where practical, permissions should not be repeatedly assigned directly to individual users.

This improves:

* scalability;
* access review;
* consistency;
* auditing; and
* least-privilege management.

---

# 19. Privileged Identity Architecture

Standard and privileged identities should remain separate.

Example:

```text
john.smith
    |
    +----> Email
    +----> Files
    +----> Standard Applications


adm.john.smith
    |
    +----> AD Administration
    +----> Server Administration
    +----> Approved Security Tasks
```

The privileged account should not normally be used for:

* web browsing;
* email;
* general productivity;
* routine document access; or
* unrelated daily activities.

---

# 20. Privilege Boundary

The transition from standard access to administrative access represents a major security boundary.

```text
STANDARD TRUST
     |
     | Privileged Authentication
     v
ADMINISTRATIVE TRUST
     |
     v
AD / Servers / Security Configuration
```

Crossing this boundary should require an explicitly authorised privileged identity.

---

# 21. Domain Controller Security Architecture

DC01 should receive stronger protection than ordinary member systems.

Security controls will include, where feasible:

* restricted administrator access;
* restricted interactive logon;
* limited installed software;
* host firewall;
* security baseline GPOs;
* patching;
* Microsoft Defender;
* advanced audit policy;
* PowerShell logging;
* account-change auditing;
* privileged-group monitoring; and
* DNS protection.

The design principle is:

> Domain Controllers should not be treated as general-purpose servers.

---

# 22. Group Policy Security Architecture

Group Policy will provide centralised Windows security enforcement.

Security GPOs will be designed around functions such as:

```text
Domain Security
│
├── Authentication Policy
├── Account Lockout
├── Audit Policy
├── Domain Controller Security
├── Server Security
├── Workstation Security
├── Windows Defender
├── Windows Firewall
├── PowerShell Logging
├── User Rights
└── Additional Hardening
```

The exact GPO structure will be designed during the Group Policy security phase.

---

# 23. GPO Validation Architecture

Configuration alone is insufficient.

The project will validate policy application using tools such as:

```powershell
gpresult /r
```

and:

```powershell
gpresult /h GPResult.html
```

The validation chain will be:

```text
Security Requirement
        ↓
GPO Created
        ↓
GPO Linked
        ↓
Policy Processed
        ↓
GPResult
        ↓
Configuration Test
        ↓
Evidence
```

---

# 24. Network Security Architecture

Network communication should be controlled according to:

* source;
* destination;
* protocol;
* port;
* business requirement;
* security requirement; and
* trust level.

The design model is:

```text
SOURCE
   ↓
Security Requirement
   ↓
PFSENSE01
   ↓
ALLOW / DENY
   ↓
DESTINATION
```

---

# 25. Firewall Design Principle

Firewall policy should move toward:

**Deny by Default + Explicitly Allow Required Communication**

Each important rule should eventually have:

* Rule ID
* Source
* Destination
* Protocol
* Port
* Action
* Business justification
* Security justification
* Related threat
* Related security objective
* Test procedure
* Evidence reference

Example:

```text
Rule:
NET-FW-OT-001

Source:
Corporate Zone

Destination:
OT Zone

Action:
DENY

Threat:
THR-OT-001

Objective:
SEC-OBJ-15

Validation:
Attempt unauthorised CLIENT01 → OT01 connection
```

The detailed traffic matrix will be created in Task 02.5.

---

# 26. Host-Based Security Architecture

Because the Corporate / Management zone contains systems with different trust levels, network segmentation alone is insufficient.

Windows systems should also use host-based protections.

These include:

* Windows Firewall;
* Microsoft Defender;
* Group Policy;
* restricted services;
* least privilege;
* secure administrative access;
* patching;
* audit policy; and
* PowerShell logging.

This creates an additional security boundary even when systems share the same network.

---

# 27. DNS Security Architecture

DC01 currently provides DNS services required by Active Directory.

DNS security objectives include:

* reliable AD name resolution;
* restricted DNS administration;
* controlled DNS configuration;
* secure dynamic updates where applicable;
* controlled forwarding;
* DNS configuration validation; and
* monitoring where practical.

Clients participating in the domain should use the appropriate Active Directory DNS infrastructure rather than arbitrary external DNS servers for domain resolution.

---

# 28. Logging Architecture

Security logging provides the detective layer of the architecture.

Relevant sources include:

```text
DC01
  |
  +---- Authentication Events
  +---- Account Changes
  +---- Group Changes
  +---- Policy Changes
  +---- Privileged Activity


CLIENT01 / ADMIN01
  |
  +---- Logon Activity
  +---- Process Activity
  +---- PowerShell
  +---- Defender
  +---- Firewall


PFSENSE01
  |
  +---- Allowed Traffic
  +---- Blocked Traffic
  +---- Firewall Events
```

---

# 29. Security Events of Interest

The architecture should eventually provide visibility into:

* successful logons;
* failed logons;
* account lockouts;
* account creation;
* account deletion;
* password changes;
* privileged-group changes;
* administrative logons;
* policy changes;
* PowerShell execution;
* process execution where configured;
* firewall blocks; and
* security-control changes.

---

# 30. Monitoring Architecture — Lab State

The lab does not initially include an enterprise SIEM.

Therefore:

```text
Security Events
      ↓
Local Logs
      ↓
Windows Event Viewer
      +
PowerShell Queries
      +
pfSense Logs
      ↓
Security Analysis
```

Selected evidence will be exported and retained in GitHub where appropriate and safe.

---

# 31. Monitoring Architecture — Production Recommendation

A production environment should move toward:

```text
DC01 --------+
             |
Servers -----+
             |
Endpoints ---+----> Centralised Logging / SIEM
             |                |
pfSense -----+                v
                         Detection Rules
                              |
                              v
                            Alerts
                              |
                              v
                       Incident Response
```

Centralised logging improves correlation, detection, retention, and investigation.

---

# 32. Backup Security Architecture

Backup infrastructure should be considered part of the security architecture rather than only an IT operations function.

The intended design is:

```text
Production Systems
       |
       | Required Backup Traffic Only
       v
    PFSENSE01
       |
       v
  BACKUP ZONE
       |
    BACKUP01
```

Access to BACKUP01 should be limited to explicitly required:

* backup communication;
* administrative communication; and
* recovery operations.

---

# 33. Ransomware Resilience Architecture

The architecture is designed to reduce the impact of a ransomware-style attack.

```text
Initial Compromise
       ↓
    CLIENT01
       ↓
Credential Theft
       ↓
Privilege Escalation
       ↓
Lateral Movement
       ↓
AD Compromise Attempt
       |
       +-------------------+
       |                   |
       v                   v
  OT Boundary         Backup Boundary
       |                   |
       X                   X
       |                   |
     OT01              BACKUP01
```

The goal is not to assume that every earlier defensive control will succeed.

The goal is to provide multiple opportunities to:

* prevent;
* detect;
* contain; and
* recover.

---

# 34. Security Control Layers

The architecture uses multiple security-control layers.

| Layer             | Example Controls                            |
| ----------------- | ------------------------------------------- |
| Virtualisation    | VirtualBox network isolation                |
| Network           | pfSense, firewall rules                     |
| Host              | Windows Firewall, Defender, hardening       |
| Identity          | Active Directory, groups, least privilege   |
| Privileged Access | Separate admin accounts                     |
| Configuration     | Group Policy                                |
| Authentication    | Password and lockout controls               |
| Detection         | Windows logs, PowerShell logs, pfSense logs |
| Validation        | GPResult, PowerShell, security testing      |
| Recovery          | Backup isolation                            |
| Governance        | Risk assessment and framework mapping       |

---

# 35. Preventative, Detective and Recovery Controls

The project intentionally uses different types of security controls.

## Preventative

Examples:

* least privilege;
* firewall rules;
* GPO hardening;
* password policy;
* Windows Firewall;
* separate admin accounts.

## Detective

Examples:

* failed-logon auditing;
* privileged-group monitoring;
* PowerShell logging;
* Windows Security logs;
* pfSense firewall logs.

## Corrective / Recovery

Examples:

* account disablement;
* configuration remediation;
* backup restoration;
* firewall isolation; and
* system rebuild.

A mature security architecture requires more than preventative controls.

---

# 36. Architecture Against Primary Attack Path

The primary threat-model attack path is:

```text
Credential Theft
      ↓
Corporate User
      ↓
CLIENT01
      ↓
Privilege Escalation
      ↓
Privileged Account
      ↓
DC01
      ↓
Domain Compromise
      |
      +------------+
      |            |
      v            v
    OT01       BACKUP01
```

The architecture introduces controls at multiple stages:

```text
Credential Theft
      ↓
[Authentication Controls]
      ↓
CLIENT01
      ↓
[Endpoint Hardening]
      ↓
[Least Privilege]
      ↓
Privilege Escalation
      ↓
[Admin Account Separation]
      ↓
[Privileged Access Controls]
      ↓
DC01
      ↓
[DC Hardening + Auditing]
      ↓
Domain Compromise
      |
      +---------------------+
      |                     |
[pfSense OT Rules]    [pfSense Backup Rules]
      |                     |
      X                     X
      |                     |
    OT01                BACKUP01
```

This demonstrates defence in depth against a defined attack path.

---

# 37. Current Architecture vs Target Architecture

The project distinguishes between:

## Current Lab Architecture

What can realistically be implemented with available home-lab resources.

and:

## Production Target Architecture

What would be recommended for a real BlueWave environment.

The current architecture is:

```text
Corporate / Management
        +
Servers
        +
Domain Controller
        +
User Systems
```

within a combined zone.

The production target would provide stronger separation.

---

# 38. Production Architecture Direction

A more mature architecture could include:

```text
                    SECURITY GATEWAY
                          |
       +------------------+------------------+
       |          |       |       |          |
       v          v       v       v          v

 Management     Domain   Server Corporate    Guest
   Zone       Controller  Zone    User       Zone
                Zone              Zone
       |
       +------------------------------+
                                      |
                               Additional Boundaries
                                      |
                       +--------------+-------------+
                       |                            |
                       v                            v
                    OT Zone                    Backup Zone
```

Additional production controls could include:

* redundant Domain Controllers;
* dedicated administrative workstations;
* MFA;
* privileged access management;
* centralised SIEM;
* EDR/XDR;
* vulnerability management;
* enterprise patch management;
* protected backup platforms;
* network intrusion detection; and
* stronger network segmentation.

---

# 39. Architecture Risk Decisions

The architecture contains intentional compromises because of lab constraints.

These should be treated as documented risk decisions rather than hidden weaknesses.

Example:

```text
Constraint:
VirtualBox adapter limitations

        ↓

Decision:
Use three primary security zones

        ↓

Risk:
Reduced internal segmentation

        ↓

Compensating Controls:
Windows Firewall
GPO hardening
Least privilege
pfSense boundaries

        ↓

Production Recommendation:
More granular VLAN segmentation
```

---

# 40. Architecture Traceability

Architecture components should eventually be traceable to security requirements.

Example:

```text
THR-OT-001
Corporate-to-OT Lateral Movement
        ↓
SEC-OBJ-15
Protect Manufacturing Systems
        ↓
ARCH-NET-OT
Dedicated OT Security Zone
        ↓
CTRL-NET-OT
pfSense Restrictive Firewall Policy
        ↓
TEST-NET-OT
Corporate → OT Connectivity Test
        ↓
EVID-NET-OT
Firewall + Test Evidence
```

---

# 41. Initial Architecture Component IDs

The following identifiers will be used to support future traceability.

| ID           | Architecture Component                |
| ------------ | ------------------------------------- |
| ARCH-NET-001 | pfSense Security Gateway              |
| ARCH-NET-002 | Corporate / Management Zone           |
| ARCH-NET-003 | Manufacturing / OT Zone               |
| ARCH-NET-004 | Backup Zone                           |
| ARCH-AD-001  | Active Directory Domain               |
| ARCH-AD-002  | Domain Controller — DC01              |
| ARCH-AD-003  | Active Directory DNS                  |
| ARCH-IAM-001 | User Identity Structure               |
| ARCH-IAM-002 | Security Group Structure              |
| ARCH-PAM-001 | Separate Privileged Accounts          |
| ARCH-GPO-001 | Centralised Security GPO Architecture |
| ARCH-END-001 | Corporate Endpoint Security           |
| ARCH-ADM-001 | Administrative System                 |
| ARCH-LOG-001 | Windows Security Logging              |
| ARCH-LOG-002 | pfSense Firewall Logging              |
| ARCH-BAK-001 | Protected Backup Zone                 |

These identifiers may be expanded as detailed architecture is developed.

---

# 42. Architecture Validation Strategy

Each major architectural control should eventually have a validation method.

| Architecture Component      | Example Validation                     |
| --------------------------- | -------------------------------------- |
| Corporate → OT Boundary     | Blocked connectivity test              |
| Corporate → Backup Boundary | Blocked connectivity test              |
| Privileged Access           | Standard account cannot administer AD  |
| Security Groups             | Authorised vs unauthorised access test |
| Group Policy                | GPResult                               |
| Windows Firewall            | Port/connectivity test                 |
| Authentication Controls     | Controlled authentication test         |
| Logging                     | Generate event and verify log          |
| PowerShell Logging          | Execute test command and inspect logs  |
| Backup Isolation            | Network access test                    |

---

# 43. Architecture Evidence Strategy

Evidence should demonstrate both configuration and operation.

Examples include:

```text
Architecture Requirement
        ↓
Configuration Evidence
        +
Operational Test
        +
Security Log
        ↓
Control Evidence
```

For example:

```text
OT Isolation
    ↓
pfSense Rule Screenshot
    +
CLIENT01 → OT01 Failed Test
    +
pfSense Block Log
    ↓
Validated Segmentation Evidence
```

This is stronger than relying on a firewall screenshot alone.

---

# 44. Relationship to Frameworks

Detailed framework mapping will occur later in the project.

However, the architecture is intended to support security areas associated with:

## ISO/IEC 27001:2022

Including areas such as:

* identity management;
* access control;
* privileged access;
* secure configuration;
* logging;
* monitoring;
* network security;
* network segregation;
* backup; and
* vulnerability management.

## NIST Cybersecurity Framework 2.0

Across:

* Govern;
* Identify;
* Protect;
* Detect;
* Respond; and
* Recover.

## ASD Essential Eight

Particularly:

* restrict administrative privileges;
* patch operating systems;
* application control where implemented;
* user application hardening;
* MFA where feasible; and
* regular backups.

Detailed mappings must be based on controls actually implemented and validated.

---

# 45. Architecture Review Triggers

This architecture should be reviewed when:

* a new virtual machine is introduced;
* security zones change;
* network interfaces change;
* pfSense architecture changes;
* another Domain Controller is deployed;
* Active Directory structure changes significantly;
* privileged-access design changes;
* new administrative protocols are enabled;
* backup architecture changes;
* monitoring infrastructure changes;
* new security tooling is introduced; or
* security testing identifies an unexpected attack path.

---

# 46. Architecture Success Criteria

The architecture will be considered successfully implemented when:

* critical systems are identified;
* security zones are documented;
* trust boundaries are documented;
* Active Directory is treated as critical security infrastructure;
* privileged access is separated from standard access;
* security policies are centrally managed;
* unnecessary cross-zone communication is restricted;
* OT is protected from unrestricted corporate access;
* backup infrastructure is isolated;
* endpoints and servers are hardened;
* security-relevant activity is logged;
* controls are technically validated;
* evidence is retained;
* residual architectural risks are documented; and
* production recommendations are clearly distinguished from lab implementation.

---

# 47. Key Architecture Principle

The architecture is not designed around the assumption that every attack can be prevented.

Instead, it asks:

> **If this system or account is compromised, what prevents the attacker from reaching the next critical asset?**

For example:

```text
CLIENT01 compromised
        ↓
Can attacker become administrator?
        ↓
Can attacker reach DC01?
        ↓
Can attacker obtain privileged credentials?
        ↓
Can attacker cross into OT?
        ↓
Can attacker reach backups?
        ↓
Can the activity be detected?
        ↓
Can BlueWave recover?
```

This approach connects identity security, network security, monitoring, and recovery into a single defence-in-depth architecture.

---

# 48. Document Status

| Field                         | Value                                                  |
| ----------------------------- | ------------------------------------------------------ |
| Project                       | Active-Directory-Cybersecurity-Lab                     |
| Document                      | Security Architecture Overview                         |
| Organisation                  | BlueWave Manufacturing Pty Ltd                         |
| Architecture Model            | Defence in Depth / Assume Breach                       |
| Identity Platform             | Microsoft Active Directory                             |
| Security Gateway              | pfSense                                                |
| Virtualisation                | Oracle VirtualBox                                      |
| Security Zones                | Corporate / Management, Manufacturing / OT, Backup     |
| Critical Identity Asset       | DC01                                                   |
| Primary Network Controls      | pfSense + Windows Firewall                             |
| Primary Configuration Control | Group Policy                                           |
| Primary Validation Methods    | GPResult, PowerShell, Event Logs, Connectivity Testing |
| Status                        | High-Level Security Architecture Defined               |

