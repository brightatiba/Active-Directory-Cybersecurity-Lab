# Active Directory Cybersecurity Lab — Security Objectives

## 1. Document Purpose

This document defines the cybersecurity objectives for the **Active-Directory-Cybersecurity-Lab**.

The objectives establish the security outcomes that the lab is intended to achieve and provide the foundation for later:

* security architecture decisions;
* risk assessments;
* technical security controls;
* Group Policy configurations;
* firewall rules;
* logging and monitoring;
* security testing;
* evidence collection;
* remediation activities; and
* cybersecurity framework mapping.

The project uses the following security lifecycle:

**Security Objective → Threat/Risk → Security Control → Technical Implementation → Validation → Evidence → Framework Mapping → Residual Risk**

Each major security objective is assigned a unique identifier to support traceability throughout the project.

---

# 2. Security Objective Classification

Security objectives are grouped into the following domains:

| Domain | Focus                                  |
| ------ | -------------------------------------- |
| IAM    | Identity and Access Management         |
| PAM    | Privileged Access Management           |
| AD     | Active Directory Security              |
| AUTH   | Authentication Security                |
| GPO    | Group Policy and Secure Configuration  |
| NET    | Network Security and Segmentation      |
| OT     | Manufacturing / OT Protection          |
| BAK    | Backup and Recovery Protection         |
| DNS    | DNS Security                           |
| END    | Endpoint and Infrastructure Protection |
| LOG    | Logging and Monitoring                 |
| TEST   | Security Validation and Testing        |
| AUTO   | PowerShell Security Automation         |
| RISK   | Risk and Vulnerability Management      |
| GOV    | Governance, Compliance and Evidence    |

---

# 3. Identity and Access Management

## SEC-OBJ-01 — Centralise Identity Management

### Objective

Provide centralised management of user, computer, and administrative identities through Microsoft Active Directory.

### Security Rationale

Decentralised or unmanaged identities increase the likelihood of:

* orphaned accounts;
* inconsistent permissions;
* weak access controls;
* poor accountability; and
* unauthorised access.

Centralised identity management allows security policies and access controls to be applied consistently.

### Expected Controls

Controls may include:

* Active Directory Domain Services;
* centrally managed user accounts;
* centrally managed computer accounts;
* organisational units;
* security groups;
* account lifecycle processes; and
* centralised Group Policy.

### Validation Examples

Evidence may include:

* Active Directory Users and Computers;
* OU structure;
* user and computer objects;
* security-group membership;
* PowerShell queries; and
* screenshots.

---

## SEC-OBJ-02 — Enforce Role-Based Access

### Objective

Grant access to organisational resources according to business role and job requirements rather than assigning permissions directly to individual users wherever practical.

### Security Rationale

Direct and inconsistent permission assignment can create excessive access and make access reviews difficult.

Security groups provide a more manageable method of implementing role-based access.

### Expected Controls

Controls may include:

* department security groups;
* role-based groups;
* resource-specific groups;
* NTFS permissions;
* least-privilege access; and
* periodic membership review.

### Validation Examples

* security-group membership review;
* PowerShell group queries;
* access testing;
* NTFS permission inspection; and
* screenshots.

---

## SEC-OBJ-03 — Apply Least Privilege

### Objective

Ensure users, administrators, systems, and services receive only the permissions required to perform authorised activities.

### Security Rationale

Excessive privileges increase the potential impact of:

* credential compromise;
* malicious insiders;
* malware;
* privilege escalation; and
* administrative mistakes.

### Expected Controls

* restricted group membership;
* limited administrative rights;
* role-based security groups;
* controlled server access;
* restricted local administrator rights; and
* privileged-account separation.

### Validation Examples

* group membership review;
* local administrator review;
* access-control testing;
* PowerShell security scripts; and
* privilege comparison tests.

---

# 4. Privileged Access Security

## SEC-OBJ-04 — Separate Administrative and Standard Accounts

### Objective

Ensure privileged administrators use separate identities for standard user activities and administrative activities.

Example:

```text
Standard account:
john.smith

Privileged account:
adm.john.smith
```

### Security Rationale

Using Domain Administrator or other highly privileged accounts for everyday activities unnecessarily exposes privileged credentials.

Activities such as:

* web browsing;
* email;
* document access; and
* general workstation use

should not normally require highly privileged credentials.

### Expected Controls

* separate administrator accounts;
* separate standard user accounts;
* restricted privileged-group membership;
* administrative account naming conventions; and
* documented privileged-access procedures.

### Validation Examples

* privileged-group membership;
* account inspection;
* PowerShell queries;
* administrative access testing; and
* screenshots.

---

## SEC-OBJ-05 — Restrict Privileged Group Membership

### Objective

Limit membership of highly privileged Active Directory groups to explicitly authorised administrative accounts.

### Security Rationale

Groups such as Domain Admins provide extensive control over the environment.

Unnecessary membership significantly increases the consequences of account compromise.

### Expected Controls

Review and restrict membership of groups such as:

* Domain Admins;
* Enterprise Admins where applicable;
* Schema Admins;
* Administrators; and
* other delegated administrative groups.

### Validation Examples

PowerShell may be used to inspect membership:

```powershell
Get-ADGroupMember "Domain Admins"
```

Evidence will include exported or documented group membership.

---

## SEC-OBJ-06 — Reduce Privileged Credential Exposure

### Objective

Reduce the number of systems and activities where privileged credentials are used.

### Security Rationale

Privileged credentials used on compromised or lower-trust systems may become exposed to credential theft or reuse attacks.

### Expected Controls

* separate administrative identities;
* restricted administrative logon;
* dedicated administrative systems where feasible;
* least privilege;
* controlled remote administration; and
* security logging.

---

# 5. Authentication Security

## SEC-OBJ-07 — Enforce Secure Password Controls

### Objective

Implement domain password controls appropriate to the security requirements of the environment.

### Security Rationale

Weak or predictable passwords increase exposure to:

* password guessing;
* brute-force attacks;
* password spraying; and
* credential compromise.

### Expected Controls

Controls may include:

* minimum password length;
* password complexity requirements where appropriate;
* password history;
* account lockout controls; and
* secure handling of privileged passwords.

### Validation Examples

* Group Policy inspection;
* GPResult;
* PowerShell;
* controlled password testing; and
* account policy output.

---

## SEC-OBJ-08 — Reduce Automated Authentication Attacks

### Objective

Reduce the effectiveness of repeated unauthorised authentication attempts.

### Security Rationale

Attackers frequently use automated credential attacks against user accounts.

### Expected Controls

* account lockout policy;
* authentication logging;
* monitoring of failed logons;
* password security; and
* detection of repeated failures.

### Validation Examples

* controlled failed-login testing;
* Windows Security Event Logs;
* account lockout events; and
* monitoring evidence.

---

# 6. Active Directory Infrastructure Security

## SEC-OBJ-09 — Protect Domain Controllers

### Objective

Treat Domain Controllers as highly trusted security infrastructure and minimise their attack surface.

### Security Rationale

Domain Controllers contain and process security-critical information including:

* authentication data;
* directory information;
* security groups;
* Group Policy information; and
* privileged identities.

Compromise of a Domain Controller may result in compromise of the Active Directory domain.

### Expected Controls

* restricted administrative access;
* security hardening;
* limited installed software;
* patch management;
* firewall protection;
* logging;
* secure Group Policy;
* restricted interactive logon; and
* monitoring.

---

## SEC-OBJ-10 — Protect Active Directory Configuration

### Objective

Prevent unauthorised or unnecessary modification of Active Directory security configuration.

### Security Rationale

Changes to:

* privileged groups;
* user accounts;
* Group Policy;
* OUs; and
* security permissions

can significantly alter the security posture of the domain.

### Expected Controls

* privileged access restrictions;
* delegated administration;
* least privilege;
* auditing;
* security logging; and
* change validation.

---

# 7. Group Policy and Secure Configuration

## SEC-OBJ-11 — Establish Centralised Security Hardening

### Objective

Use Group Policy to centrally enforce security configuration across appropriate Windows systems.

### Security Rationale

Manual configuration can result in inconsistent security settings and configuration drift.

Group Policy allows security controls to be applied consistently across managed systems.

### Expected Controls

Security GPOs may address:

* password policy;
* account lockout;
* Windows Defender;
* Windows Firewall;
* audit policy;
* PowerShell logging;
* removable media;
* local administrator controls;
* user rights;
* security options;
* SMB security; and
* other Windows hardening settings.

---

## SEC-OBJ-12 — Validate Group Policy Application

### Objective

Verify that intended security GPOs are actually applied to target systems and users.

### Security Rationale

Creating a GPO does not guarantee that the security configuration has been successfully applied.

Problems may result from:

* incorrect OU placement;
* security filtering;
* inheritance;
* conflicting GPOs;
* WMI filters;
* replication issues; or
* configuration errors.

### Expected Controls and Evidence

Validation methods may include:

```powershell
gpresult /r
```

and:

```powershell
gpresult /h GPResult.html
```

Evidence should demonstrate the policies actually applied to the target system or user.

---

# 8. Network Security

## SEC-OBJ-13 — Enforce Network Segmentation

### Objective

Separate systems with different security requirements into defined security zones and control communication between those zones.

### Security Rationale

A flat network allows attackers to move more easily between compromised systems.

Segmentation reduces the potential attack path and limits lateral movement.

### Security Zones

The lab uses:

1. Corporate / Management Zone
2. Manufacturing / OT Zone
3. Backup Zone

### Expected Controls

* VirtualBox network separation;
* pfSense interfaces;
* routing controls;
* firewall rules; and
* controlled inter-zone communication.

---

## SEC-OBJ-14 — Apply Least-Privilege Network Access

### Objective

Permit only network communication required for authorised system or business functions.

### Security Rationale

Unnecessary network connectivity expands the attack surface and provides additional lateral-movement opportunities.

### Expected Approach

Where practical:

**Default Deny → Explicitly Allow Required Traffic**

Firewall rules should document:

* source;
* destination;
* protocol;
* port/service;
* business or technical justification; and
* security rationale.

---

# 9. Manufacturing / OT Security

## SEC-OBJ-15 — Protect Manufacturing Systems from Corporate Compromise

### Objective

Prevent unrestricted access from the Corporate / Management environment to Manufacturing / OT systems.

### Security Rationale

Corporate endpoints are commonly exposed to threats such as:

* phishing;
* malicious attachments;
* web-based attacks;
* credential theft; and
* ransomware.

Compromise of a corporate endpoint should not automatically provide unrestricted access to manufacturing systems.

### Expected Controls

* dedicated OT security zone;
* pfSense firewall enforcement;
* restricted communication paths;
* controlled administrative access; and
* connectivity testing.

---

# 10. Backup Security

## SEC-OBJ-16 — Isolate Backup Infrastructure

### Objective

Protect backup infrastructure from unnecessary access by corporate and manufacturing systems.

### Security Rationale

Attackers and ransomware operators may attempt to destroy or encrypt backups before disrupting production systems.

Compromise of a standard corporate account should not automatically provide access to backup infrastructure.

### Expected Controls

* dedicated Backup security zone;
* restrictive firewall rules;
* limited administrative access;
* least privilege;
* logging; and
* controlled backup communication.

---

## SEC-OBJ-17 — Preserve Recovery Capability

### Objective

Ensure security incidents affecting production systems do not automatically eliminate the organisation's ability to recover.

### Security Rationale

Backup security is not only an availability issue.

Recovery infrastructure is a cybersecurity control supporting resilience against:

* ransomware;
* destructive malware;
* accidental deletion;
* malicious administrators; and
* system failure.

---

# 11. DNS Security

## SEC-OBJ-18 — Protect Internal DNS Services

### Objective

Provide controlled and reliable DNS services for the Active Directory environment.

### Security Rationale

Active Directory depends heavily on DNS.

DNS misconfiguration or compromise can affect:

* authentication;
* Domain Controller discovery;
* service discovery;
* Group Policy;
* system connectivity; and
* administrative operations.

### Expected Controls

* Active Directory-integrated DNS where applicable;
* restricted DNS administration;
* controlled DNS configuration;
* secure forwarding;
* appropriate dynamic-update configuration; and
* DNS logging or monitoring where practical.

---

# 12. Endpoint and Infrastructure Protection

## SEC-OBJ-19 — Harden Windows Systems

### Objective

Reduce the attack surface of Windows servers and workstations through secure configuration.

### Expected Controls

Controls may include:

* Windows Firewall;
* Microsoft Defender;
* secure Group Policy;
* removal or restriction of unnecessary services;
* secure protocol configuration;
* patching;
* local administrator restrictions; and
* security auditing.

---

## SEC-OBJ-20 — Maintain Supported and Patched Systems

### Objective

Reduce exposure to known vulnerabilities by maintaining appropriate operating-system and application updates.

### Security Rationale

Known vulnerabilities may allow attackers to:

* execute code;
* escalate privileges;
* bypass security controls; or
* move laterally.

### Evidence

Evidence may include:

* patch status;
* Windows Update history;
* PowerShell queries;
* screenshots; and
* vulnerability findings.

---

# 13. Logging and Monitoring

## SEC-OBJ-21 — Generate Security-Relevant Audit Logs

### Objective

Configure systems to record events necessary for security monitoring and investigation.

### Relevant Events

Logging should provide visibility into events such as:

* successful authentication;
* failed authentication;
* account lockout;
* account creation;
* account deletion;
* security-group changes;
* privileged-group membership changes;
* administrative logon;
* policy changes;
* process execution where configured;
* PowerShell activity; and
* other security-sensitive events.

---

## SEC-OBJ-22 — Detect Suspicious Authentication Activity

### Objective

Provide sufficient logging to identify suspicious authentication behaviour.

### Examples

Potential indicators include:

* repeated failed logons;
* locked accounts;
* unusual privileged logons;
* repeated authentication attempts; and
* unexpected administrative activity.

---

## SEC-OBJ-23 — Monitor Privileged Changes

### Objective

Provide visibility into changes that affect privileged access or critical security configuration.

### Examples

Monitor changes involving:

* Domain Admin membership;
* administrator accounts;
* security groups;
* Group Policy; and
* other sensitive Active Directory objects.

---

# 14. Security Validation and Testing

## SEC-OBJ-24 — Validate Security Control Effectiveness

### Objective

Test security controls to determine whether they operate as intended.

### Security Rationale

A configured control is not necessarily an effective control.

Testing converts configuration into evidence.

### Validation Methods

Testing may include:

* GPResult validation;
* authentication tests;
* firewall connectivity tests;
* access-control tests;
* group membership tests;
* DNS tests;
* logging tests;
* PowerShell queries; and
* configuration inspection.

---

## SEC-OBJ-25 — Test Network Isolation

### Objective

Verify that prohibited communication between security zones is blocked while authorised communication remains available.

### Example Test

```text
CLIENT01
    |
    | Attempt unauthorised connection
    v
PFSENSE01
    |
    X BLOCK
    |
   OT01
```

Evidence may include:

* failed connectivity tests;
* firewall logs;
* pfSense rule hits; and
* screenshots.

---

## SEC-OBJ-26 — Test Access-Control Boundaries

### Objective

Verify that users without appropriate permissions cannot access protected resources.

### Testing Examples

* standard user attempting administrative activity;
* unauthorised department user attempting file access;
* standard account attempting privileged access;
* unauthorised system attempting cross-zone communication.

Both successful authorised access and failed unauthorised access should be tested where practical.

---

# 15. PowerShell Security Automation

## SEC-OBJ-27 — Automate Security Assessment Tasks

### Objective

Use PowerShell to improve repeatability and efficiency of security assessment activities.

### Potential Scripts

Scripts may assess:

* Domain Admin membership;
* privileged groups;
* disabled accounts;
* inactive accounts;
* password-related account properties;
* group membership;
* computer inventory;
* security configuration;
* event logs; and
* Active Directory security information.

---

## SEC-OBJ-28 — Produce Repeatable Security Evidence

### Objective

Use scripts and command output to create repeatable evidence rather than relying exclusively on manual screenshots.

### Security Rationale

Repeatable evidence improves:

* consistency;
* auditability;
* reassessment;
* troubleshooting; and
* remediation validation.

---

# 16. Risk and Vulnerability Management

## SEC-OBJ-29 — Identify Security Weaknesses

### Objective

Identify vulnerabilities, insecure configurations, architectural weaknesses, and control gaps within the lab.

Potential findings may include:

* excessive privilege;
* weak password policy;
* unnecessary firewall access;
* inadequate logging;
* insecure protocols;
* poor account configuration;
* insufficient segmentation; and
* unsupported or unpatched systems.

---

## SEC-OBJ-30 — Assess Cybersecurity Risk

### Objective

Evaluate identified security weaknesses according to their likelihood and potential business impact.

Risk assessments should consider:

* affected asset;
* threat;
* vulnerability;
* existing controls;
* likelihood;
* impact;
* risk rating;
* treatment;
* control owner where appropriate; and
* residual risk.

---

## SEC-OBJ-31 — Track Security Remediation

### Objective

Document how identified security weaknesses are addressed.

The remediation lifecycle should demonstrate:

**Finding → Risk → Recommendation → Remediation → Retest → Evidence → Residual Risk**

This provides evidence of security improvement rather than simply documenting vulnerabilities.

---

# 17. Governance, Compliance and Evidence

## SEC-OBJ-32 — Maintain Security Evidence

### Objective

Maintain organised evidence demonstrating security-control implementation and effectiveness.

### Evidence Types

Evidence may include:

* screenshots;
* configuration exports;
* GPResult reports;
* PowerShell output;
* PowerShell scripts;
* Windows Event Logs;
* firewall logs;
* test results;
* risk records; and
* remediation records.

---

## SEC-OBJ-33 — Map Security Controls to Recognised Frameworks

### Objective

Map relevant technical controls and evidence against applicable requirements from:

* ISO/IEC 27001:2022;
* NIST Cybersecurity Framework 2.0; and
* ASD Essential Eight.

### Security Rationale

Technical security controls should support broader organisational cybersecurity and risk-management requirements.

Framework mapping will demonstrate the relationship between engineering implementation and GRC assurance.

The project will not claim certification or compliance solely because individual controls have been mapped to a framework.

---

## SEC-OBJ-34 — Document Residual Risk and Limitations

### Objective

Clearly identify security risks that remain after controls have been implemented.

### Security Rationale

No security control eliminates all risk.

Residual risks may remain because of:

* technical limitations;
* lab constraints;
* business requirements;
* resource constraints; or
* accepted design decisions.

Residual risk should be documented rather than hidden.

---

## SEC-OBJ-35 — Maintain Control Traceability

### Objective

Maintain traceability between security objectives, risks, controls, technical implementation, testing, evidence, and framework requirements.

### Traceability Model

```text
Security Objective
        ↓
      Risk
        ↓
     Control
        ↓
 Implementation
        ↓
      Test
        ↓
    Evidence
        ↓
Framework Mapping
        ↓
 Residual Risk
```

Example identifiers may include:

```text
SEC-OBJ-05
    ↓
RISK-AD-004
    ↓
CTRL-PAM-002
    ↓
AD / GPO Configuration
    ↓
TEST-PAM-002
    ↓
EVID-PAM-002
    ↓
ISO / NIST / Essential Eight
```

This model will be expanded during later project phases.

---

# 18. Security Objective Summary

| ID         | Security Objective                                      | Domain |
| ---------- | ------------------------------------------------------- | ------ |
| SEC-OBJ-01 | Centralise Identity Management                          | IAM    |
| SEC-OBJ-02 | Enforce Role-Based Access                               | IAM    |
| SEC-OBJ-03 | Apply Least Privilege                                   | IAM    |
| SEC-OBJ-04 | Separate Administrative and Standard Accounts           | PAM    |
| SEC-OBJ-05 | Restrict Privileged Group Membership                    | PAM    |
| SEC-OBJ-06 | Reduce Privileged Credential Exposure                   | PAM    |
| SEC-OBJ-07 | Enforce Secure Password Controls                        | AUTH   |
| SEC-OBJ-08 | Reduce Automated Authentication Attacks                 | AUTH   |
| SEC-OBJ-09 | Protect Domain Controllers                              | AD     |
| SEC-OBJ-10 | Protect Active Directory Configuration                  | AD     |
| SEC-OBJ-11 | Establish Centralised Security Hardening                | GPO    |
| SEC-OBJ-12 | Validate Group Policy Application                       | GPO    |
| SEC-OBJ-13 | Enforce Network Segmentation                            | NET    |
| SEC-OBJ-14 | Apply Least-Privilege Network Access                    | NET    |
| SEC-OBJ-15 | Protect Manufacturing Systems from Corporate Compromise | OT     |
| SEC-OBJ-16 | Isolate Backup Infrastructure                           | BAK    |
| SEC-OBJ-17 | Preserve Recovery Capability                            | BAK    |
| SEC-OBJ-18 | Protect Internal DNS Services                           | DNS    |
| SEC-OBJ-19 | Harden Windows Systems                                  | END    |
| SEC-OBJ-20 | Maintain Supported and Patched Systems                  | END    |
| SEC-OBJ-21 | Generate Security-Relevant Audit Logs                   | LOG    |
| SEC-OBJ-22 | Detect Suspicious Authentication Activity               | LOG    |
| SEC-OBJ-23 | Monitor Privileged Changes                              | LOG    |
| SEC-OBJ-24 | Validate Security Control Effectiveness                 | TEST   |
| SEC-OBJ-25 | Test Network Isolation                                  | TEST   |
| SEC-OBJ-26 | Test Access-Control Boundaries                          | TEST   |
| SEC-OBJ-27 | Automate Security Assessment Tasks                      | AUTO   |
| SEC-OBJ-28 | Produce Repeatable Security Evidence                    | AUTO   |
| SEC-OBJ-29 | Identify Security Weaknesses                            | RISK   |
| SEC-OBJ-30 | Assess Cybersecurity Risk                               | RISK   |
| SEC-OBJ-31 | Track Security Remediation                              | RISK   |
| SEC-OBJ-32 | Maintain Security Evidence                              | GOV    |
| SEC-OBJ-33 | Map Security Controls to Recognised Frameworks          | GOV    |
| SEC-OBJ-34 | Document Residual Risk and Limitations                  | GOV    |
| SEC-OBJ-35 | Maintain Control Traceability                           | GOV    |

---

# 19. Success Criteria

The security objectives will be considered successfully addressed when:

* relevant technical controls have been identified;
* controls have been implemented where technically feasible;
* implementation has been documented;
* controls have been tested;
* evidence has been collected;
* identified weaknesses have been recorded;
* remediation has been performed where appropriate;
* residual risks have been documented; and
* applicable controls have been mapped to relevant cybersecurity frameworks.

Not every security objective must result in a perfect or fully implemented control.

Where implementation is limited by the lab environment, the limitation and recommended production-state control should be documented.

---

# 20. Document Status

| Field           | Value                                                 |
| --------------- | ----------------------------------------------------- |
| Project         | Active-Directory-Cybersecurity-Lab                    |
| Document        | Security Objectives                                   |
| Organisation    | BlueWave Manufacturing Pty Ltd                        |
| Primary Focus   | Active Directory Cybersecurity                        |
| Security Model  | Defence in Depth / Least Privilege                    |
| Frameworks      | ISO/IEC 27001:2022, NIST CSF 2.0, ASD Essential Eight |
| Objective Count | 35                                                    |
| Status          | Initial Security Objectives Defined                   |

