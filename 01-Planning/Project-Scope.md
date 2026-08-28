# Active Directory Cybersecurity Lab — Project Scope

## 1. Document Purpose

This document defines the scope, objectives, boundaries, assumptions, technologies, security activities, and governance requirements for the **Active-Directory-Cybersecurity-Lab**.

The project is based on the Active Directory environment developed for the fictional **BlueWave Manufacturing Pty Ltd** Governance, Risk and Compliance (GRC) case study.

The purpose of this project is to demonstrate the design, implementation, hardening, monitoring, testing, and assessment of a Microsoft Active Directory environment from a **cybersecurity GRC perspective**.

This is not intended to be solely an IT support or system administration lab. Administrative technologies such as Active Directory Domain Services, DNS, Group Policy, Windows Server, Oracle VirtualBox, and pfSense are used as platforms through which cybersecurity controls are designed, implemented, validated, and documented.

The project follows the security lifecycle:

**Plan → Design → Implement → Harden → Monitor → Validate → Assess → Improve**

---

# 2. Business Scenario

## 2.1 Organisation

**BlueWave Manufacturing Pty Ltd** is a fictional Australian industrial manufacturing organisation used as the business context for this cybersecurity lab.

BlueWave operates a combination of corporate IT infrastructure and manufacturing/Operational Technology (OT) systems.

The organisation relies on Microsoft Active Directory to provide centralised identity and access management for users, computers, servers, administrators, and organisational resources.

Because Active Directory controls authentication, authorisation, administrative privileges, security configuration, and access to critical resources, compromise of the Active Directory environment could result in widespread organisational compromise.

Active Directory is therefore treated as **critical security infrastructure and part of the organisation's security control plane**.

---

# 3. Project Purpose

The purpose of the Active Directory Cybersecurity Lab is to demonstrate how an organisation can protect an Active Directory environment against common identity, privilege, endpoint, infrastructure, and network-based cyber threats.

The project will demonstrate both:

1. **Cybersecurity engineering** — designing and implementing technical security controls.
2. **Governance, Risk and Compliance (GRC)** — assessing risk, validating controls, collecting evidence, and mapping implemented controls against recognised cybersecurity frameworks.

---

# 4. Security Objectives

The primary security objectives of the project are to:

* Protect Active Directory from unauthorised access and misuse.
* Reduce the likelihood of credential compromise.
* Implement least-privilege access principles.
* Separate standard and privileged administrative identities.
* Reduce opportunities for privilege escalation.
* Reduce opportunities for lateral movement.
* Protect Domain Controllers and other critical infrastructure.
* Harden Windows systems using centrally managed Group Policy.
* Secure internal DNS services.
* Segment networks according to security requirements and trust levels.
* Restrict communication between security zones using pfSense firewall policies.
* Protect manufacturing/OT systems from unnecessary corporate network access.
* Protect backup infrastructure from unnecessary network exposure.
* Improve visibility through security logging and monitoring.
* Detect security-relevant authentication and administrative events.
* Validate that security controls are operating as intended.
* Automate selected security assessment and administrative tasks using PowerShell.
* Document security weaknesses and associated risks.
* Produce evidence demonstrating security-control implementation and effectiveness.
* Map implemented controls against recognised cybersecurity frameworks.

---

# 5. Security Principles

The project will apply the following security principles throughout the design and implementation.

## 5.1 Least Privilege

Users, administrators, systems, and services should receive only the permissions required to perform their authorised functions.

---

## 5.2 Separation of Privileged Access

Administrative activities should be separated from normal user activities.

Where practical, administrators will use separate accounts for:

* standard user activity; and
* privileged administrative activity.

This reduces exposure of privileged credentials and improves accountability for administrative actions.

---

## 5.3 Defence in Depth

The environment will not rely on a single security control.

Security will be implemented through multiple layers including:

* network controls;
* identity controls;
* authentication controls;
* access controls;
* endpoint configuration;
* Group Policy;
* privileged access controls;
* logging;
* monitoring;
* security testing; and
* backup and recovery controls.

Failure of one control should not automatically result in compromise of the entire environment.

---

## 5.4 Network Segmentation

Systems with different security requirements or trust levels should be logically separated where technically feasible.

Communication between security zones should be explicitly controlled using firewall rules.

---

## 5.5 Secure Configuration

Systems should be configured using secure baseline principles.

Unnecessary functionality, excessive privileges, insecure configurations, and unnecessary network communication should be removed or restricted where practical.

---

## 5.6 Accountability and Logging

Security-sensitive activities should generate sufficient logs to support:

* security monitoring;
* incident investigation;
* administrative accountability;
* troubleshooting; and
* control validation.

---

## 5.7 Validation Over Assumption

A security control will not be considered effective solely because it has been configured.

Controls should be validated using appropriate evidence such as:

* configuration inspection;
* command output;
* GPResult reports;
* PowerShell queries;
* Windows Event Logs;
* firewall logs;
* access-control tests;
* connectivity tests; and
* security testing.

---

# 6. Technology Scope

The lab uses the following core technologies.

| Technology                       | Purpose                                                                 |
| -------------------------------- | ----------------------------------------------------------------------- |
| Oracle VirtualBox                | Virtualisation platform hosting the lab environment                     |
| pfSense                          | Firewall, routing, network segmentation and traffic-control platform    |
| Microsoft Windows Server         | Active Directory and supporting infrastructure                          |
| Active Directory Domain Services | Centralised identity, authentication and authorisation                  |
| Microsoft DNS                    | Active Directory-integrated name resolution                             |
| Group Policy                     | Centralised Windows security configuration and hardening                |
| Windows Client                   | User endpoint and security-policy validation                            |
| PowerShell                       | Security administration, assessment and automation                      |
| Windows Event Logging            | Security monitoring and investigation evidence                          |
| GPResult                         | Validation of applied Group Policy configuration                        |
| GitHub                           | Project documentation, evidence organisation and portfolio presentation |

---

# 7. Network Security Scope

## 7.1 Architecture Constraint

The BlueWave lab is hosted using Oracle VirtualBox.

Because this is a resource-constrained home-lab environment and the virtualisation platform introduces practical network-adapter limitations, the enterprise-style network architecture has been simplified.

Instead of creating an individual virtual network for every business or infrastructure function, systems have been consolidated into **three primary security zones**.

This represents a deliberate lab constraint rather than the recommended architecture for a production enterprise environment.

---

# 8. Security Zones

## 8.1 Zone 1 — Corporate / Management Zone

The first security zone combines systems representing:

* IT and management;
* Active Directory servers;
* member servers;
* corporate users;
* administrative systems; and
* guest/test systems where required by the lab.

The consolidation of these systems introduces additional lateral-movement risk compared with a fully segmented production network.

This limitation will be documented and considered during security assessments.

In a production environment, BlueWave would ideally provide additional separation between:

* management infrastructure;
* Domain Controllers;
* application and file servers;
* corporate endpoints;
* administrative workstations; and
* guest/untrusted devices.

---

## 8.2 Zone 2 — Manufacturing / OT Zone

The Manufacturing/OT zone represents BlueWave's operational and industrial systems.

This zone is separated from the Corporate / Management zone to reduce the possibility that compromise of a corporate workstation or user account could immediately provide unrestricted access to manufacturing infrastructure.

Traffic between the Corporate / Management zone and Manufacturing / OT zone must pass through pfSense and is subject to firewall policy.

The OT environment is simulated and does not contain real industrial control systems.

---

## 8.3 Zone 3 — Backup Zone

The Backup zone contains systems representing BlueWave's backup and recovery infrastructure.

Backup infrastructure is treated as security-sensitive because attackers, particularly ransomware operators, may attempt to delete, modify, encrypt, or otherwise compromise backups before disrupting production systems.

The Backup zone is therefore separated from other security zones.

Communication with backup infrastructure should be restricted to explicitly required systems, protocols, and administrative activities.

---

# 9. pfSense Security Role

pfSense operates as the primary network security gateway for the lab.

Its security responsibilities include:

* routing between security zones;
* enforcing network segmentation;
* restricting unnecessary inter-zone communication;
* controlling permitted traffic;
* reducing network attack surface;
* supporting isolation of the Manufacturing/OT environment;
* supporting isolation of backup infrastructure; and
* generating firewall evidence and logs.

Firewall rules will follow the principle that communication between security zones should be permitted only where there is a documented technical or business requirement.

---

# 10. Active Directory Security Scope

The Active Directory portion of the project includes:

* Active Directory Domain Services;
* domain configuration;
* Domain Controller security;
* DNS;
* organisational units;
* user accounts;
* security groups;
* departmental access structures;
* privileged accounts;
* administrative groups;
* Group Policy;
* authentication controls;
* password and account policies;
* access control;
* Windows security configuration;
* security logging;
* administrative auditing;
* Group Policy validation;
* Active Directory security assessment; and
* PowerShell-based security checks.

---
<!--
# 11. Existing Active Directory Implementation

Several Active Directory components were developed during the original BlueWave Manufacturing GRC project and are inherited by this standalone lab.

The following components have already been implemented:

* [x] Organisational Unit structure
* [x] User accounts
* [x] Department security groups
* [x] Privileged access structure

These components will not automatically be considered secure simply because they have already been implemented.

Later phases of this project will:

1. review the existing configuration;
2. assess the associated security risks;
3. validate the design against least-privilege principles;
4. identify weaknesses or misconfigurations;
5. implement improvements where necessary; and
6. collect evidence demonstrating the resulting security posture.

-->
---

# 12. Cybersecurity Activities In Scope

The project includes the following cybersecurity engineering activities:

* Active Directory security architecture;
* identity and access management;
* privileged access management;
* least-privilege implementation;
* Windows security hardening;
* Domain Controller protection;
* Group Policy security configuration;
* password and authentication controls;
* DNS security;
* network segmentation;
* pfSense firewall policy;
* infrastructure protection;
* security logging;
* Windows security auditing;
* monitoring;
* Group Policy validation;
* security configuration validation;
* controlled security testing;
* lateral-movement control testing;
* access-control testing;
* PowerShell security scripting;
* backup infrastructure protection;
* evidence collection;
* vulnerability and configuration review; and
* remediation of identified weaknesses.

---

# 13. GRC Activities In Scope

The project also includes governance, risk and compliance activities.

These include:

* identification of cyber threats;
* identification of vulnerabilities;
* cybersecurity risk assessment;
* control identification;
* control implementation tracking;
* control validation;
* evidence collection;
* identification of residual risk;
* documentation of security limitations;
* security findings;
* remediation tracking;
* security-control mapping;
* final security assessment; and
* documentation of recommendations for a production environment.

---

# 14. Threats In Scope

The lab will consider threats including, but not limited to:

* credential theft;
* password spraying;
* brute-force authentication attempts;
* excessive user privileges;
* privilege escalation;
* misuse of administrative accounts;
* lateral movement;
* Pass-the-Hash-style credential abuse;
* Kerberos-related attacks such as Kerberoasting;
* insecure service accounts;
* Group Policy abuse;
* Active Directory misconfiguration;
* DNS manipulation;
* malicious PowerShell activity;
* unauthorised access to servers;
* unauthorised access to OT systems;
* ransomware propagation;
* backup compromise;
* security-policy misconfiguration; and
* inadequate logging or monitoring.

Security testing performed as part of this project will be limited to systems owned and operated within the isolated lab environment.

---

# 15. Systems In Scope

The anticipated lab systems include:

| System       | Role                           | Security Relevance                                           |
| ------------ | ------------------------------ | ------------------------------------------------------------ |
| PFSENSE01    | Firewall / Router              | Network segmentation and traffic enforcement                 |
| DC01         | Domain Controller / DNS        | Identity, authentication and central security infrastructure |
| FILESERVER01 | File / Member Server           | Resource access and authorisation testing                    |
| CLIENT01     | Corporate Workstation          | User security and Group Policy testing                       |
| ADMIN01      | Administrative System          | Privileged-access testing                                    |
| OT01         | Simulated Manufacturing System | OT segmentation testing                                      |
| BACKUP01     | Simulated Backup System        | Backup isolation and recovery security                       |

The exact virtual-machine inventory may change as the lab develops.

Any significant changes to the environment should be reflected in the project documentation.

---

# 16. Security Evidence In Scope

Security controls implemented in the project should generate evidence where appropriate.

Evidence may include:

* screenshots;
* Group Policy configuration;
* GPResult reports;
* PowerShell command output;
* PowerShell scripts;
* Windows Event Logs;
* firewall-rule screenshots;
* pfSense logs;
* network connectivity tests;
* authentication tests;
* access-control tests;
* security test results;
* configuration exports;
* risk-assessment records; and
* remediation evidence.

Evidence should demonstrate not only that a control was configured, but where possible that the control operates as intended.

---

# 17. Security Control Lifecycle

Security controls implemented in the lab should follow the lifecycle:

**Requirement → Threat/Risk → Security Control → Implementation → Validation → Evidence → Framework Mapping → Residual Risk**

For example:

**Risk:** Weak user passwords increase the likelihood of account compromise.

**Control:** Centralised domain password policy.

**Implementation:** Group Policy.

**Validation:** Policy inspection, GPResult and controlled account testing.

**Evidence:** GPO screenshots, GPResult report and test results.

**Governance:** Mapping to applicable security framework requirements.

This methodology connects technical implementation with security assurance and GRC requirements.

---

# 18. Compliance and Framework Scope

The project will use the following cybersecurity frameworks as reference points.

## 18.1 ISO/IEC 27001

The project will map applicable technical and administrative controls against relevant **ISO/IEC 27001:2022 Annex A controls**.

Relevant areas are expected to include:

* identity management;
* access control;
* authentication information;
* privileged access rights;
* secure configuration;
* logging;
* monitoring;
* network security;
* network segregation;
* backup; and
* vulnerability management.

The project does not claim that the lab itself is ISO/IEC 27001 certified.

Framework mapping is intended to demonstrate how technical security evidence can support an organisational Information Security Management System.

---

## 18.2 NIST Cybersecurity Framework 2.0

Security activities will be mapped where appropriate against the six NIST CSF 2.0 functions:

* **Govern**
* **Identify**
* **Protect**
* **Detect**
* **Respond**
* **Recover**

This mapping will demonstrate how Active Directory security activities contribute to broader organisational cyber-risk management.

---

## 18.3 ASD Essential Eight

The lab will assess applicable controls against the Australian Signals Directorate's Essential Eight.

Relevant areas include:

* application control;
* patch applications;
* configure Microsoft Office macro settings where applicable;
* user application hardening;
* restrict administrative privileges;
* patch operating systems;
* multi-factor authentication where technically feasible; and
* regular backups.

Because this is a resource-constrained home lab, not every Essential Eight requirement can necessarily be fully implemented.

Controls will therefore be identified as appropriate using statuses such as:

* Implemented;
* Partially Implemented;
* Not Implemented; or
* Not Applicable.

Any limitations will be documented rather than presenting unsupported claims of compliance.

---

# 19. Out of Scope

The following technologies and activities are initially outside the scope of the project:

* production Microsoft Entra ID environments;
* production Microsoft 365 environments;
* production Microsoft Defender XDR;
* production Microsoft Sentinel;
* commercial enterprise EDR platforms;
* real industrial control systems;
* production Operational Technology;
* production Public Key Infrastructure;
* multi-site Active Directory;
* Active Directory forest trusts;
* highly available enterprise infrastructure;
* production disaster-recovery infrastructure;
* real customer or employee data;
* attacks against external organisations;
* testing systems not owned or authorised for use in the lab; and
* claims of formal regulatory or framework certification.

These technologies may be considered future extensions but are not required for completion of the core project.

---

# 20. Lab Assumptions

The project operates under the following assumptions:

1. All systems are hosted in an authorised private lab environment.
2. No real BlueWave infrastructure exists; BlueWave Manufacturing is fictional.
3. No real customer or employee information will be stored in the environment.
4. Security testing will target only systems within the lab.
5. Virtual machines may represent enterprise systems at a reduced scale.
6. Some enterprise security technologies may be simulated or documented rather than fully deployed.
7. The environment prioritises demonstrating security concepts rather than enterprise-scale performance or availability.
8. Lab constraints will be documented when they prevent implementation of a recommended security control.

---

# 21. Lab Limitations

The environment has several known limitations.

These include:

* limited host computing resources;
* Oracle VirtualBox networking constraints;
* limited virtual network adapters;
* a simplified three-zone architecture;
* combined corporate, server and management infrastructure;
* limited number of Domain Controllers;
* lack of enterprise high availability;
* simulated OT infrastructure;
* simulated backup infrastructure; and
* limited access to commercial enterprise security tooling.

These limitations create security differences between the lab and a production environment.

They will therefore be considered during risk assessment and final recommendations.

---
<!--
# 22. Lab Architecture vs Production Architecture

The implemented lab architecture should not be interpreted as the ideal production architecture for BlueWave Manufacturing.

For example, the current Corporate / Management zone combines systems that would preferably be further segmented in a mature production environment.

A production design could separate:

* Domain Controllers;
* management infrastructure;
* administrative workstations;
* application servers;
* file servers;
* corporate endpoints;
* guest networks;
* manufacturing/OT networks; and
* backup infrastructure.

Additional security technologies could also include:

* dedicated privileged-access workstations;
* redundant Domain Controllers;
* enterprise endpoint detection and response;
* centralised SIEM;
* network intrusion detection;
* vulnerability-management platforms;
* MFA-backed administrative access;
* enterprise backup and recovery platforms; and
* dedicated management networks.

The lab architecture represents a balance between cybersecurity design principles and practical home-lab constraints.

-->
---

# 23. Project Success Criteria

The project will be considered successful when:

* the Active Directory architecture is fully documented;
* identity structures have been security reviewed;
* privileged access follows documented security principles;
* Windows security baselines have been implemented through Group Policy;
* critical infrastructure has been appropriately hardened;
* network zones are protected by documented pfSense firewall policies;
* unnecessary inter-zone communication is restricted;
* security logging and auditing have been implemented;
* security controls have been technically validated;
* GPResult evidence demonstrates appropriate Group Policy application;
* PowerShell scripts support security assessment or automation;
* security tests demonstrate whether controls operate as expected;
* significant security risks have been documented;
* remediation actions have been recorded;
* technical controls have been mapped against applicable framework requirements;
* limitations and residual risks have been clearly documented; and
* the GitHub repository provides sufficient evidence for another cybersecurity professional to understand the project's security decisions.

---

# 24. Expected Project Outcome

At completion, the Active-Directory-Cybersecurity-Lab will demonstrate the relationship between:

**Cyber Threats**

↓

**Risk**

↓

**Security Architecture**

↓

**Technical Controls**

↓

**Security Hardening**

↓

**Monitoring**

↓

**Testing and Validation**

↓

**Evidence**

↓

**Risk Treatment**

↓

**Governance and Compliance**

The final repository should demonstrate not simply that an Active Directory environment was built, but that it was **designed, secured, tested, assessed, and documented using cybersecurity and risk-management principles**.

---
<!--
# 25. Scope Review

The scope should be reviewed whenever significant changes are made to:

* lab architecture;
* security zones;
* virtual machines;
* Active Directory design;
* privileged-access model;
* security tooling;
* testing methodology; or
* compliance-framework coverage.

Changes that materially alter the security boundary should be reflected in this document and relevant architecture documentation.
-->
---

## Document Status

| Field             | Value                                                 |
| ----------------- | ----------------------------------------------------- |
| Project           | Active-Directory-Cybersecurity-Lab                    |
| Organisation      | BlueWave Manufacturing Pty Ltd                        |
| Environment       | Cybersecurity Home Lab                                |
| Virtualisation    | Oracle VirtualBox                                     |
| Network Security  | pfSense                                               |
| Identity Platform | Microsoft Active Directory                            |
| Primary Focus     | Active Directory Cybersecurity                        |
| Frameworks        | ISO/IEC 27001:2022, NIST CSF 2.0, ASD Essential Eight |
| Status            | Initial Scope Defined                                 |

