# Active Directory Cybersecurity Lab — Lab Limitations

## 1. Document Purpose

This document identifies the technical, architectural, operational, and resource limitations of the **Active-Directory-Cybersecurity-Lab**.

The purpose is to distinguish between:

* what can realistically be implemented in the home lab;
* what would normally be expected in a production enterprise environment;
* the security risks introduced by those limitations;
* compensating controls used within the lab; and
* recommended improvements for a production deployment.

Documenting limitations is important because a technically functional lab should not be presented as equivalent to a production-grade security architecture.

The project therefore uses the following model:

**Limitation → Security Risk → Lab Compensating Control → Production Recommendation**

---

# 2. Lab Environment Context

The project is hosted using **Oracle VirtualBox** and represents the Active Directory environment of the fictional BlueWave Manufacturing Pty Ltd.

The lab includes:

* Microsoft Active Directory;
* Windows Server;
* Windows client systems;
* pfSense;
* DNS;
* Group Policy;
* security zones;
* firewall rules;
* security logging;
* security testing;
* PowerShell automation; and
* governance and control-assurance activities.

Because the lab runs on limited home computing resources, some enterprise security capabilities must be simplified, simulated, or documented rather than fully implemented.

---

# 3. Core Limitation Summary

| ID     | Limitation                                    | Primary Security Impact                              |
| ------ | --------------------------------------------- | ---------------------------------------------------- |
| LIM-01 | Simplified three-zone network                 | Reduced internal segmentation                        |
| LIM-02 | Combined Corporate / Management / Server zone | Increased lateral-movement exposure                  |
| LIM-03 | Limited VirtualBox network adapters           | Restricts enterprise-style network design            |
| LIM-04 | Single Domain Controller                      | No AD redundancy or high availability                |
| LIM-05 | Limited server inventory                      | Some roles must be consolidated or simulated         |
| LIM-06 | Simulated OT environment                      | Does not represent real ICS/industrial complexity    |
| LIM-07 | Simulated backup infrastructure               | Limited recovery realism                             |
| LIM-08 | No enterprise SIEM                            | Reduced centralised detection capability             |
| LIM-09 | No commercial EDR/XDR platform                | Reduced endpoint detection capability                |
| LIM-10 | Limited MFA capability                        | Some privileged access remains password dependent    |
| LIM-11 | Limited privileged access infrastructure      | No full enterprise PAM platform                      |
| LIM-12 | Limited vulnerability-management tooling      | More manual assessment required                      |
| LIM-13 | No enterprise PKI                             | Certificate-based security use cases limited         |
| LIM-14 | No high availability                          | Single-system failure may disrupt services           |
| LIM-15 | Limited patch-management infrastructure       | Patch process may be manually validated              |
| LIM-16 | Small-scale user population                   | Does not reproduce enterprise identity scale         |
| LIM-17 | Limited log retention                         | Reduced historical investigation capability          |
| LIM-18 | Home network dependency                       | Lab perimeter depends partly on external environment |
| LIM-19 | No real business data                         | Data-security scenarios are simulated                |
| LIM-20 | Resource constraints                          | Limits concurrent services and security tooling      |

---

# 4. LIM-01 — Simplified Three-Zone Network Architecture

## Limitation

The lab uses three primary security zones:

1. Corporate / Management
2. Manufacturing / OT
3. Backup

This architecture is simpler than a mature enterprise network.

## Security Risk

Combining multiple system types into broad security zones can increase:

* lateral movement opportunities;
* unnecessary network exposure;
* trust between unrelated systems; and
* potential attack blast radius.

## Lab Compensating Controls

The lab reduces this risk using:

* pfSense firewall rules;
* host-based Windows Firewall;
* least privilege;
* Group Policy hardening;
* privileged-account separation;
* logging and monitoring; and
* controlled connectivity testing.

## Production Recommendation

A production environment should consider separate network segments for:

* Domain Controllers;
* management systems;
* administrative workstations;
* application servers;
* file servers;
* corporate endpoints;
* guest systems;
* OT systems;
* backup infrastructure; and
* other sensitive workloads.

Inter-segment communication should be controlled through explicit firewall policy.

---

# 5. LIM-02 — Combined Corporate / Management / Server Zone

## Limitation

The current architecture places multiple system types within the same primary security zone, including:

* IT and management systems;
* Domain Controllers;
* servers;
* corporate user systems; and
* administrative systems.

## Security Risk

A compromised corporate workstation may have greater network visibility of servers and administrative infrastructure than would be desirable in production.

This increases the possibility of:

* lateral movement;
* service discovery;
* credential attacks;
* administrative interface exposure; and
* compromise of critical infrastructure.

## Lab Compensating Controls

The lab uses:

* Windows Firewall;
* least-privilege access;
* separate administrator accounts;
* restricted privileged groups;
* Group Policy security hardening;
* logging;
* authentication monitoring; and
* system-specific access restrictions.

## Production Recommendation

Create separate security zones or VLANs for:

* Domain Controllers;
* server infrastructure;
* corporate endpoints;
* administrative systems; and
* guest devices.

Use restrictive firewall rules between those segments.

---

# 6. LIM-03 — Oracle VirtualBox Network Adapter Constraints

## Limitation

Oracle VirtualBox introduces practical constraints on the number and design of network adapters used by virtual machines.

This influenced the decision to simplify the BlueWave architecture into three major security zones.

## Security Risk

The lab cannot fully reproduce the segmentation expected in a larger enterprise.

This may result in:

* broader trust zones;
* fewer network enforcement points; and
* reduced realism when testing inter-VLAN security.

## Lab Compensating Controls

The project documents the limitation explicitly and focuses on:

* strong pfSense enforcement between the three available zones;
* host firewall controls;
* identity-based controls;
* security testing; and
* documenting the preferred production architecture.

## Production Recommendation

Use enterprise virtualisation or physical network infrastructure supporting:

* dedicated VLANs;
* virtual switches;
* multiple network interfaces;
* trunking;
* dedicated management networks; and
* scalable segmentation.

---

# 7. LIM-04 — Single Domain Controller

## Limitation

The core lab initially relies on a single Domain Controller, DC01.

## Security Risk

A single Domain Controller creates:

* a single point of failure;
* reduced resilience;
* limited recovery flexibility;
* no Active Directory service redundancy; and
* greater impact if DC01 becomes unavailable.

## Lab Compensating Controls

The project focuses on:

* hardening DC01;
* restricting administrative access;
* applying secure Group Policy;
* patching;
* logging;
* DNS protection;
* configuration documentation; and
* recovery planning.

## Production Recommendation

A production environment should deploy at least two appropriately protected Domain Controllers where business requirements justify redundancy.

Additional considerations include:

* separate hosts;
* tested backup and recovery;
* DNS redundancy;
* secure replication;
* site design; and
* resilient authentication services.

---

# 8. LIM-05 — Limited Server Inventory

## Limitation

The home lab cannot host every server role that may exist in a real enterprise environment.

Some systems may be:

* consolidated;
* represented by a single virtual machine; or
* simulated through documentation and testing.

## Security Risk

Role consolidation can:

* increase attack surface;
* combine trust levels;
* create larger failure domains; and
* reduce architectural realism.

## Lab Compensating Controls

The lab will:

* document each system role;
* minimise unnecessary services;
* apply server-specific hardening;
* use least privilege; and
* identify where consolidation would not be recommended in production.

## Production Recommendation

Separate critical server roles where:

* security requirements differ;
* availability requirements differ;
* privilege boundaries are important; or
* regulatory/business requirements justify isolation.

---

# 9. LIM-06 — Simulated Manufacturing / OT Environment

## Limitation

The Manufacturing / OT zone is simulated and does not contain real:

* PLCs;
* SCADA platforms;
* HMIs;
* engineering workstations;
* safety systems; or
* industrial controllers.

## Security Risk

The lab cannot fully reproduce:

* industrial protocol behaviour;
* safety implications;
* vendor-specific OT constraints;
* legacy operating systems;
* deterministic network requirements; or
* real production consequences.

## Lab Compensating Controls

The lab focuses on broadly applicable OT security principles:

* network isolation;
* restrictive firewall rules;
* controlled administrative access;
* least privilege;
* logging;
* attack-path analysis; and
* corporate-to-OT lateral-movement testing.

## Production Recommendation

A real manufacturing environment should perform dedicated OT security architecture and risk assessment including:

* asset inventory;
* industrial protocol analysis;
* safety requirements;
* zone-and-conduit design;
* vendor access controls;
* remote-access restrictions;
* monitoring; and
* industrial incident-response planning.

---

# 10. LIM-07 — Simulated Backup Infrastructure

## Limitation

BACKUP01 represents backup infrastructure but does not reproduce a complete enterprise backup platform.

## Security Risk

The lab may not fully represent:

* immutable storage;
* offline backups;
* backup orchestration;
* enterprise retention;
* multiple recovery tiers;
* off-site replication; or
* large-scale restore testing.

## Lab Compensating Controls

The project will demonstrate:

* backup-zone isolation;
* restricted network access;
* privileged-access controls;
* backup security risk analysis;
* logging; and
* recovery validation where feasible.

## Production Recommendation

Production backup architecture should consider:

* immutable backups;
* offline or logically isolated backup copies;
* separate administrative credentials;
* MFA;
* restricted backup management networks;
* encryption;
* off-site copies;
* regular restore testing; and
* documented recovery objectives.

---

# 11. LIM-08 — No Enterprise SIEM Platform

## Limitation

The lab does not initially use a full enterprise Security Information and Event Management platform.

## Security Risk

Without centralised logging, security events may be:

* distributed across individual systems;
* harder to correlate;
* harder to retain;
* slower to investigate; and
* more difficult to monitor continuously.

## Lab Compensating Controls

The project will use available sources such as:

* Windows Security Event Logs;
* PowerShell logs;
* Group Policy evidence;
* pfSense logs;
* manual event analysis; and
* PowerShell-based log queries.

## Production Recommendation

Deploy centralised security logging using a SIEM or equivalent platform.

A mature design should support:

* log aggregation;
* correlation;
* alerting;
* retention;
* access control;
* investigation; and
* incident response.

---

# 12. LIM-09 — No Commercial EDR/XDR Platform

## Limitation

The lab does not initially include a commercial enterprise Endpoint Detection and Response or Extended Detection and Response platform.

## Security Risk

This limits visibility into:

* process behaviour;
* malicious execution;
* endpoint telemetry;
* advanced credential theft;
* persistence;
* lateral movement; and
* attacker behaviour.

## Lab Compensating Controls

The lab will rely on available Windows security capabilities such as:

* Microsoft Defender where available;
* Windows Firewall;
* Windows Event Logs;
* PowerShell logging;
* Group Policy hardening;
* process auditing where configured; and
* controlled security testing.

## Production Recommendation

Deploy an appropriately managed EDR/XDR solution capable of:

* endpoint telemetry;
* behavioural detection;
* threat investigation;
* isolation;
* response actions; and
* centralised monitoring.

---

# 13. LIM-10 — Limited Multi-Factor Authentication

## Limitation

Some Active Directory authentication scenarios in the lab may rely primarily on username and password authentication.

## Security Risk

Password-only authentication is more vulnerable to:

* phishing;
* password reuse;
* password spraying;
* credential theft; and
* compromised administrator credentials.

## Lab Compensating Controls

The project reduces exposure using:

* stronger password controls;
* account lockout;
* separate privileged accounts;
* least privilege;
* restricted administrative access;
* monitoring; and
* limited privileged logon locations.

## Production Recommendation

Require phishing-resistant MFA where feasible, particularly for:

* administrators;
* remote access;
* privileged systems;
* cloud services; and
* security-sensitive applications.

---

# 14. LIM-11 — No Full Enterprise Privileged Access Management Platform

## Limitation

The lab implements privileged-access principles but does not initially deploy a dedicated enterprise PAM platform.

## Security Risk

This limits capabilities such as:

* password vaulting;
* automatic credential rotation;
* session recording;
* approval workflows;
* just-in-time administration;
* just-enough administration; and
* privileged session monitoring.

## Lab Compensating Controls

The project will demonstrate:

* separate administrator accounts;
* restricted group membership;
* least privilege;
* dedicated administrative systems where feasible;
* privileged logging; and
* access reviews.

## Production Recommendation

Consider enterprise PAM capabilities for sensitive administrative access.

---

# 15. LIM-12 — Limited Vulnerability Management Tooling

## Limitation

The project may not include a complete commercial vulnerability-management platform.

## Security Risk

Vulnerabilities may be:

* identified less frequently;
* reviewed manually;
* harder to prioritise;
* harder to track centrally; or
* missed entirely.

## Lab Compensating Controls

The project will use:

* patch review;
* PowerShell;
* Windows configuration inspection;
* security baselines;
* manual assessment;
* controlled security tests; and
* risk tracking.

## Production Recommendation

Use a managed vulnerability-management program including:

* authenticated scanning;
* asset coverage;
* vulnerability prioritisation;
* remediation ownership;
* exception tracking;
* reporting; and
* retesting.

---

# 16. LIM-13 — No Enterprise PKI

## Limitation

The lab does not initially deploy a complete enterprise Public Key Infrastructure.

## Security Risk

The project cannot fully demonstrate controls relying on:

* certificate-based device authentication;
* smart-card authentication;
* internal certificate lifecycle management;
* enterprise TLS certificates;
* certificate auto-enrolment; or
* advanced certificate-based access control.

## Lab Compensating Controls

PKI-dependent controls will be documented as future enhancements where applicable.

## Production Recommendation

Deploy enterprise PKI only where justified by security and operational requirements, with appropriate:

* CA hierarchy;
* key protection;
* certificate lifecycle management;
* revocation;
* access control; and
* monitoring.

---

# 17. LIM-14 — Limited High Availability

## Limitation

Most lab services run as individual virtual machines without high availability.

## Security Risk

Failure of a virtual machine or host may result in:

* service interruption;
* loss of authentication;
* DNS outage;
* monitoring gaps; or
* temporary loss of access to resources.

## Lab Compensating Controls

The project will:

* document configurations;
* maintain recoverable VM states where appropriate;
* maintain backup copies where feasible; and
* record recovery procedures.

## Production Recommendation

Design availability according to business impact and recovery requirements.

Potential measures include:

* redundant Domain Controllers;
* clustered services;
* resilient storage;
* redundant networking;
* backup power;
* tested disaster recovery; and
* geographic redundancy where justified.

---

# 18. LIM-15 — Limited Patch Management Infrastructure

## Limitation

The lab may not include enterprise patch-management systems such as centralised update orchestration.

## Security Risk

Patch compliance may depend on:

* manual update checks;
* inconsistent timing;
* manual validation; and
* limited reporting.

## Lab Compensating Controls

The project will:

* document patch status;
* verify operating-system updates;
* review update history;
* use PowerShell where appropriate; and
* record remediation evidence.

## Production Recommendation

Use centralised patch-management processes with:

* inventory;
* prioritisation;
* staged deployment;
* testing;
* compliance reporting;
* exception handling; and
* remediation tracking.

---

# 19. LIM-16 — Small-Scale User and Device Population

## Limitation

The lab contains a relatively small number of:

* users;
* groups;
* computers;
* servers; and
* administrative accounts.

## Security Risk

The project cannot fully reproduce challenges associated with:

* thousands of identities;
* complex delegation;
* nested groups;
* legacy permissions;
* mergers;
* stale accounts;
* large numbers of endpoints; or
* complex organisational structures.

## Lab Compensating Controls

The lab focuses on designing processes and structures that would scale conceptually.

Examples include:

* naming standards;
* OU design;
* group-based access;
* least privilege;
* PowerShell automation; and
* repeatable evidence collection.

## Production Recommendation

Use lifecycle governance, automation, identity governance, periodic access reviews, and scalable administration processes.

---

# 20. LIM-17 — Limited Log Retention

## Limitation

Local virtual machines may have limited disk capacity for long-term log retention.

## Security Risk

Older security events may be overwritten or unavailable during investigation.

## Lab Compensating Controls

The project will:

* identify security-relevant logs;
* increase log sizes where appropriate;
* export evidence where required;
* use screenshots and reports;
* preserve selected test evidence; and
* document retention limitations.

## Production Recommendation

Centralise logs using defined retention policies based on:

* security requirements;
* investigation needs;
* legal requirements;
* regulatory requirements; and
* business risk.

---

# 21. LIM-18 — Home Network Dependency

## Limitation

The pfSense WAN interface connects to an existing home network rather than a dedicated enterprise internet edge.

## Security Risk

The lab is partially dependent on the security and availability of the surrounding home environment.

This does not reproduce a real enterprise perimeter including:

* redundant internet connections;
* enterprise edge firewalls;
* DDoS protection;
* secure remote access;
* DMZ services; or
* dedicated upstream monitoring.

## Lab Compensating Controls

The lab uses pfSense to isolate the internal lab architecture from the surrounding network.

No production business services are exposed.

## Production Recommendation

Use dedicated perimeter architecture appropriate to organisational risk, including secure:

* edge firewalling;
* remote access;
* external service hosting;
* monitoring;
* resilience; and
* segmentation.

---

# 22. LIM-19 — No Real Business Data

## Limitation

The lab contains fictional users, systems, and information.

## Security Risk

The project cannot reproduce all operational and regulatory considerations associated with:

* personally identifiable information;
* financial records;
* employee information;
* customer information;
* intellectual property; or
* regulated data.

## Lab Compensating Controls

Synthetic data is used so that security testing can occur without exposing real information.

## Production Recommendation

Data security requirements should be based on:

* classification;
* sensitivity;
* regulatory requirements;
* business impact;
* access requirements; and
* retention obligations.

---

# 23. LIM-20 — Compute and Resource Constraints

## Limitation

The number of concurrently running virtual machines and security tools is limited by:

* CPU;
* memory;
* disk capacity;
* network resources; and
* host performance.

## Security Risk

Certain controls may not be continuously available.

Examples include:

* advanced monitoring;
* redundant infrastructure;
* multiple Domain Controllers;
* large-scale scanning; and
* dedicated security appliances.

## Lab Compensating Controls

The project prioritises controls based on learning and security value.

Systems may be started only when required for testing.

## Production Recommendation

Security architecture should be supported by infrastructure sized according to:

* user population;
* transaction volume;
* availability requirements;
* security tooling;
* monitoring requirements; and
* future growth.

---

# 24. Key Architectural Limitation — Internal Segmentation

The most significant architectural limitation in the current lab is the consolidation of multiple infrastructure roles into the Corporate / Management zone.

A simplified view is:

```text
Current Lab

Corporate / Management Zone
│
├── DC01
├── FILESERVER01
├── CLIENT01
└── ADMIN01
```

A more mature production architecture might instead resemble:

```text
Production Concept

Management Zone
│
└── Administrative Systems

Domain Controller Zone
│
├── DC01
└── DC02

Server Zone
│
└── Application / File Servers

Corporate User Zone
│
└── User Workstations

Guest Zone
│
└── Untrusted Devices

OT Zone
│
└── Manufacturing Systems

Backup Zone
│
└── Protected Backup Infrastructure
```

The lab therefore focuses additional security attention on host controls and identity-based security within the combined zone.

---

# 25. Compensating Control Strategy

Where enterprise-level architecture cannot be reproduced, the project will use compensating controls.

Examples include:

| Limitation                    | Compensating Control                          |
| ----------------------------- | --------------------------------------------- |
| Limited network segmentation  | Windows Firewall + least privilege            |
| Single Domain Controller      | Hardening + backup + documentation            |
| No EDR                        | Defender + logging + GPO hardening            |
| No SIEM                       | Local logs + PowerShell + exported evidence   |
| Limited MFA                   | Stronger privileged-account controls          |
| No PAM                        | Separate admin accounts + restricted groups   |
| Limited vulnerability tooling | Manual review + PowerShell + patch validation |
| Simulated OT                  | Firewall isolation + attack-path testing      |
| Simulated backup platform     | Dedicated zone + restricted access            |
| Limited HA                    | Recovery documentation and backups            |

A compensating control does not necessarily remove the underlying limitation.

It reduces risk while acknowledging that the preferred production control is unavailable.

---

# 26. Risk Acceptance

Some risks will remain because the project intentionally operates within home-lab constraints.

These risks should not be silently ignored.

They should be handled using one of the following approaches:

* mitigate;
* avoid;
* transfer where conceptually applicable; or
* accept.

For this lab, risk acceptance may be appropriate when:

* the cost of implementing the full enterprise control is unreasonable;
* the technology is unavailable;
* the learning benefit is limited;
* the control cannot be realistically reproduced; or
* the remaining risk is acceptable within an isolated lab.

Accepted risks will be documented during the formal risk-assessment phase.

---

# 27. Production Recommendation Principle

For every significant limitation, the project should be able to answer:

> **If this were a real BlueWave production environment, what would I recommend instead?**

This ensures the project demonstrates cybersecurity judgement rather than simply documenting technical constraints.

For example:

```text
Lab:
Single Domain Controller

Production:
Multiple hardened Domain Controllers
+ resilient DNS
+ secure backup
+ monitoring
+ tested recovery
```

or:

```text
Lab:
Combined Corporate / Server Zone

Production:
Separate user, server, management,
Domain Controller, guest, OT,
and backup security zones
```

---

# 28. Relationship to Threat Model

Lab limitations can increase the likelihood or impact of threats already identified in `Threat-Model.md`.

Examples include:

| Limitation                     | Related Threat                                      |
| ------------------------------ | --------------------------------------------------- |
| Combined corporate/server zone | THR-AD-005 — Lateral Movement                       |
| Limited network segmentation   | THR-NET-001 — Unrestricted Inter-Zone Communication |
| Single Domain Controller       | THR-AD-006 — Domain Controller Compromise           |
| No enterprise PAM              | THR-AD-004 — Privileged Account Compromise          |
| No EDR                         | THR-END-001 — Corporate Workstation Compromise      |
| No SIEM                        | THR-LOG-001 — Insufficient Security Logging         |
| Limited MFA                    | THR-AD-001 — Credential Theft                       |
| Simulated backup environment   | THR-BAK-001 — Backup Infrastructure Compromise      |

This relationship will be developed further during the formal risk-assessment phase.

---

# 29. Relationship to Security Objectives

Limitations should not prevent the project from demonstrating the intended security principles.

Instead, they determine how completely individual objectives can be implemented.

Examples:

```text
SEC-OBJ-13
Enforce Network Segmentation

Lab Constraint:
Only three major security zones

Lab Response:
Strong segmentation between those three zones

Production Recommendation:
More granular segmentation
```

```text
SEC-OBJ-22
Detect Suspicious Authentication Activity

Lab Constraint:
No enterprise SIEM

Lab Response:
Windows Event Log analysis

Production Recommendation:
Centralised SIEM detection and alerting
```

---

# 30. Limitation Status Model

Later in the project, limitations may be assigned one of the following statuses:

| Status                    | Meaning                                                    |
| ------------------------- | ---------------------------------------------------------- |
| Open                      | Limitation remains present                                 |
| Partially Mitigated       | Compensating controls reduce the associated risk           |
| Mitigated                 | Limitation has been sufficiently addressed                 |
| Accepted                  | Risk intentionally accepted for the lab                    |
| Future Enhancement        | Recommended for a future version of the project            |
| Production Recommendation | Intended for enterprise implementation rather than the lab |

This provides a structured way to track improvements.

---

# 31. Security Assurance Principle

The project will not claim that a limitation has been eliminated when only a compensating control has been implemented.

For example:

> Windows Firewall does not make a combined user/server network equivalent to dedicated network segmentation.

Similarly:

> Local Windows logs do not provide the same detection capability as a properly designed enterprise SIEM.

Accurate documentation of these differences is part of the project's GRC and security-assurance approach.

---

# 32. Interview Perspective

The architecture should be explainable in terms of both:

### What was implemented

The technical solution that was practical within the lab.

and:

### What should be implemented in production

The security architecture that would be recommended to a real organisation.

This distinction demonstrates the ability to work with technical constraints without lowering the expected security standard.

---

# 33. Future Improvement Opportunities

Potential future enhancements include:

* second Domain Controller;
* dedicated administrative workstation;
* additional internal security zones;
* dedicated Domain Controller network;
* separate corporate-user network;
* separate server network;
* guest isolation;
* centralised Windows event collection;
* SIEM deployment;
* enhanced endpoint detection;
* MFA;
* Local Administrator Password Solution;
* enterprise-style vulnerability scanning;
* protected or immutable backups;
* improved PowerShell monitoring;
* additional security-testing tools;
* PKI;
* additional Active Directory security controls; and
* automated compliance assessment.

Future enhancements should be prioritised based on security value rather than simply adding technology.

---

# 34. Review Triggers

This document should be reviewed when:

* the VirtualBox architecture changes;
* new virtual machines are introduced;
* new security zones are created;
* a second Domain Controller is deployed;
* new security tooling is introduced;
* monitoring capability changes;
* backup architecture changes;
* privileged access architecture changes;
* MFA becomes available;
* security testing identifies a limitation not previously documented; or
* a lab constraint is removed.

---

# 35. Key Takeaway

The Active-Directory-Cybersecurity-Lab is intentionally a constrained environment.

Its purpose is not to claim that a home lab reproduces a complete enterprise network.

Its purpose is to demonstrate the ability to:

* recognise architectural weaknesses;
* understand the risks they introduce;
* implement practical compensating controls;
* validate those controls;
* document residual risk; and
* recommend stronger production-state security architecture.

The guiding principle is:

**Implement what is practical in the lab, understand what is missing, mitigate the resulting risk where possible, and clearly explain what should change in production.**

---

# 36. Document Status

| Field                         | Value                                                    |
| ----------------------------- | -------------------------------------------------------- |
| Project                       | Active-Directory-Cybersecurity-Lab                       |
| Document                      | Lab Limitations                                          |
| Organisation                  | BlueWave Manufacturing Pty Ltd                           |
| Virtualisation                | Oracle VirtualBox                                        |
| Network Architecture          | Three Security Zones                                     |
| Primary Constraint            | Resource and Virtualisation Limitations                  |
| Primary Compensating Controls | pfSense, Windows Firewall, GPO, Least Privilege, Logging |
| Limitations Identified        | 20                                                       |
| Status                        | Initial Limitations Defined                              |

