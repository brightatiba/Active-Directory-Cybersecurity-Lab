# Active Directory Cybersecurity Lab — Threat Model

## 1. Document Purpose

This document defines the initial cybersecurity threat model for the **Active-Directory-Cybersecurity-Lab** based on the fictional BlueWave Manufacturing Pty Ltd environment.

The purpose of the threat model is to identify:

* critical assets;
* trust boundaries;
* potential threat actors;
* attack surfaces;
* likely attack techniques;
* potential attack paths;
* security consequences;
* preventative controls;
* detective controls;
* recovery controls; and
* security objectives associated with each threat.

The threat model will guide later security architecture, hardening, monitoring, testing, risk assessment, and control-mapping activities.

The model follows the principle:

**Asset → Threat → Attack Path → Security Impact → Control → Detection → Validation**

---

# 2. Threat-Modelling Approach

This project uses a practical, risk-based threat-modelling approach rather than attempting to reproduce a full enterprise threat-modelling methodology.

For each significant threat, the project considers:

1. **What asset is being targeted?**
2. **Who or what could attack it?**
3. **How could the attacker gain initial access?**
4. **How could the attacker increase their privileges?**
5. **How could the attacker move laterally?**
6. **What security boundary could be crossed?**
7. **What would the attacker ultimately attempt to compromise?**
8. **What controls should prevent or limit the attack?**
9. **What controls should detect the attack?**
10. **How can those controls be validated?**

The threat model is expected to evolve as the lab is implemented and additional weaknesses or attack paths are discovered.

---

# 3. Environment Overview

The lab represents a simplified enterprise Active Directory environment protected by pfSense.

The primary security zones are:

```text
                         HOME NETWORK
                              |
                              |
                         pfSense WAN
                              |
                       +-------------+
                       |  PFSENSE01  |
                       |   Firewall  |
                       +------+------+
                              |
              +---------------+---------------+
              |               |               |
              |               |               |
       CORPORATE /         OT /           BACKUP
       MANAGEMENT       MANUFACTURING       ZONE
           ZONE             ZONE
              |               |               |
      +-------+-------+      OT01         BACKUP01
      |       |       |
     DC01 FILESERVER01 CLIENT01
      |
   ADMIN01
```

pfSense represents the primary network trust-boundary enforcement point between the three security zones.

---

# 4. Critical Assets

Threat modelling begins by identifying assets that require protection.

## 4.1 Active Directory

**Criticality: Critical**

Active Directory provides centralised:

* identity;
* authentication;
* authorisation;
* security groups;
* computer management;
* Group Policy; and
* privileged administration.

Compromise of Active Directory could allow an attacker to gain widespread control over the Windows environment.

---

## 4.2 Domain Controller — DC01

**Criticality: Critical**

DC01 hosts critical identity infrastructure including:

* Active Directory Domain Services;
* authentication services;
* directory information;
* security-group information;
* Group Policy information; and
* DNS services.

DC01 is considered one of the highest-value targets in the environment.

---

## 4.3 Privileged Accounts

**Criticality: Critical**

Privileged identities may provide access to:

* Active Directory;
* Domain Controllers;
* servers;
* Group Policy;
* security configuration; and
* other administrative functions.

Examples include:

```text
adm.john.smith
```

rather than using:

```text
john.smith
```

for administrative activity.

Compromise of a privileged identity may provide an attacker with a direct path toward domain compromise.

---

## 4.4 pfSense — PFSENSE01

**Criticality: Critical**

PFSENSE01 controls communication between security zones.

Compromise or misconfiguration of the firewall could undermine:

* network segmentation;
* OT isolation;
* backup isolation;
* traffic restrictions; and
* network security monitoring.

---

## 4.5 File Server — FILESERVER01

**Criticality: High**

The file server represents organisational information and departmental resources.

Potential security concerns include:

* unauthorised data access;
* excessive permissions;
* ransomware;
* privilege misuse; and
* lateral movement.

---

## 4.6 Corporate Workstations

**Criticality: Medium to High**

Corporate endpoints such as CLIENT01 represent likely initial-access targets.

Endpoints may be exposed to:

* phishing;
* malicious documents;
* malware;
* credential theft;
* malicious PowerShell;
* user error; and
* browser-based attacks.

A compromised workstation may become an attacker's initial foothold.

---

## 4.7 Administrative Systems

**Criticality: High**

Administrative systems such as ADMIN01 may be used to perform privileged activities.

These systems require additional protection because privileged credentials may be entered or used on them.

---

## 4.8 Manufacturing / OT Systems

**Criticality: Critical**

OT01 represents manufacturing systems supporting BlueWave's industrial operations.

Compromise could potentially affect:

* production;
* availability;
* safety;
* equipment;
* product delivery; and
* business operations.

The simulated OT environment is therefore treated as a separate security zone.

---

## 4.9 Backup Infrastructure

**Criticality: Critical**

BACKUP01 represents systems responsible for maintaining recoverable copies of organisational information.

Compromise of backup infrastructure could prevent recovery following:

* ransomware;
* destructive malware;
* system failure;
* malicious administration; or
* accidental data loss.

---

## 4.10 Security Logs and Evidence

**Criticality: High**

Security logs provide evidence of:

* authentication;
* account changes;
* privilege changes;
* administrative activity;
* PowerShell execution;
* firewall decisions; and
* other security-relevant events.

Attackers may attempt to delete or manipulate logs to conceal activity.

---

# 5. Security Properties to Protect

The project considers the traditional security objectives of:

## Confidentiality

Information should only be accessible to authorised identities.

Examples:

* Active Directory information;
* organisational files;
* privileged credentials;
* backup data; and
* security configuration.

---

## Integrity

Systems and information should not be modified without authorisation.

Examples:

* Group Policy;
* firewall rules;
* security-group membership;
* DNS configuration;
* Active Directory objects; and
* backups.

---

## Availability

Critical services should remain accessible when required.

Examples:

* Active Directory;
* DNS;
* file services;
* manufacturing systems; and
* recovery infrastructure.

---

## Authentication

The environment must reliably establish the identity of users and systems.

---

## Authorisation

Authenticated identities must only receive access appropriate to their approved role.

---

## Accountability

Security-sensitive actions should be attributable to identifiable users or systems through appropriate logging and auditing.

---

# 6. Threat Actors

The project considers several potential threat-actor categories.

## 6.1 External Cybercriminal

Motivations may include:

* ransomware;
* financial gain;
* credential theft;
* data theft; and
* extortion.

Potential entry methods include:

* phishing;
* password spraying;
* credential reuse;
* exploitation of vulnerable systems; and
* malware.

---

## 6.2 Ransomware Operator

The attacker may attempt to:

1. gain initial access;
2. steal credentials;
3. escalate privileges;
4. compromise Active Directory;
5. move laterally;
6. locate backup infrastructure;
7. disable recovery capability; and
8. encrypt or disrupt organisational systems.

This is one of the primary attack scenarios considered by the project.

---

## 6.3 Malicious Insider

An authorised user may deliberately misuse legitimate access.

Potential actions include:

* accessing unauthorised information;
* stealing organisational data;
* modifying information;
* abusing privileges; and
* disrupting systems.

Least privilege and logging are particularly important for reducing insider risk.

---

## 6.4 Compromised Standard User

A legitimate user account may become controlled by an attacker.

The attacker initially inherits the permissions of the compromised account and may then attempt to:

* discover systems;
* identify privileged users;
* access shared resources;
* steal additional credentials;
* escalate privileges; and
* move laterally.

---

## 6.5 Compromised Administrator

A privileged account represents a substantially higher-impact threat.

An attacker controlling an administrative identity may attempt to:

* modify Active Directory;
* create accounts;
* change group membership;
* modify GPOs;
* disable security controls;
* access servers; or
* establish persistence.

---

## 6.6 Accidental Administrator Error

Not all security incidents are malicious.

An administrator may accidentally:

* create an overly permissive firewall rule;
* assign excessive group membership;
* configure an insecure GPO;
* disable logging;
* expose backup systems; or
* create incorrect DNS configuration.

Configuration review and validation therefore form part of the security model.

---

# 7. Trust Boundaries

A trust boundary exists where systems or identities with different security requirements interact.

The lab contains several important trust boundaries.

---

## TB-01 — Home Network to Lab Firewall

```text
Home Network
     |
     | TB-01
     v
PFSENSE01
```

The pfSense WAN interface separates the lab environment from the external/home network.

---

## TB-02 — Corporate / Management to Manufacturing / OT

```text
Corporate Zone
      |
      | TB-02
      v
   PFSENSE01
      |
      v
    OT Zone
```

Traffic crossing this boundary must be explicitly controlled.

This boundary is important because compromise of corporate IT should not automatically result in compromise of manufacturing infrastructure.

---

## TB-03 — Corporate / Management to Backup

```text
Corporate Zone
      |
      | TB-03
      v
   PFSENSE01
      |
      v
 Backup Zone
```

This boundary protects recovery infrastructure from unnecessary corporate network access.

---

## TB-04 — Standard User to Privileged Administration

```text
Standard User
     |
     | TB-04
     v
Privileged Access
     |
     v
Active Directory
```

This is an identity-based trust boundary.

Crossing this boundary should require explicitly authorised privileged credentials.

---

## TB-05 — Workstation to Domain Controller

```text
CLIENT01
    |
    | TB-05
    v
   DC01
```

Workstations require legitimate access to Active Directory services, but should not receive unrestricted administrative access to Domain Controllers.

---

## TB-06 — Administrator to Security Configuration

```text
Administrator
      |
      | TB-06
      v
AD / GPO / Firewall / Servers
```

Administrative access to security-critical configuration must be restricted and auditable.

---

# 8. Primary Attack Surface

Potential attack surfaces within the lab include:

* user credentials;
* privileged credentials;
* Windows authentication;
* Active Directory;
* Kerberos;
* NTLM where present;
* DNS;
* SMB;
* PowerShell;
* Windows Remote Management where enabled;
* Remote Desktop where enabled;
* administrative interfaces;
* shared folders;
* firewall administration;
* Group Policy;
* service accounts;
* Windows services;
* unpatched software;
* unnecessary network ports; and
* insecure configuration.

Not every attack surface will necessarily be present or enabled.

The project will document actual exposure as implementation progresses.

---

# 9. Threat Catalogue

## THR-AD-001 — Credential Theft

### Target

User and privileged credentials.

### Attack Scenario

An attacker obtains credentials through methods such as:

* phishing;
* malware;
* credential dumping;
* password reuse;
* insecure credential handling; or
* compromised systems.

### Potential Impact

Stolen credentials may allow:

* unauthorised domain access;
* file access;
* lateral movement;
* privilege escalation; or
* further credential theft.

### Relevant Security Objectives

* SEC-OBJ-03 — Apply Least Privilege
* SEC-OBJ-04 — Separate Administrative and Standard Accounts
* SEC-OBJ-06 — Reduce Privileged Credential Exposure
* SEC-OBJ-07 — Enforce Secure Password Controls
* SEC-OBJ-21 — Generate Security-Relevant Audit Logs

### Security Controls

Potential controls include:

* secure password policy;
* least privilege;
* account separation;
* endpoint hardening;
* restricted administrative logon;
* authentication logging; and
* user security awareness outside the technical lab scope.

---

## THR-AD-002 — Password Spraying and Authentication Attacks

### Target

Active Directory user accounts.

### Attack Scenario

An attacker attempts common or previously compromised passwords against multiple user accounts.

### Potential Impact

Successful authentication may provide an initial foothold in the domain.

### Relevant Security Objectives

* SEC-OBJ-07
* SEC-OBJ-08
* SEC-OBJ-21
* SEC-OBJ-22

### Security Controls

* password policy;
* account lockout policy;
* authentication logging;
* failed-logon monitoring; and
* controlled account management.

### Detection Opportunities

Repeated failed logons may generate detectable Windows Security events.

---

## THR-AD-003 — Privilege Escalation

### Target

Administrative privileges.

### Attack Scenario

An attacker controlling a standard user account attempts to obtain elevated permissions.

Potential causes include:

* excessive group membership;
* weak local administrator controls;
* insecure services;
* misconfigured permissions;
* credential exposure; or
* insecure delegation.

### Potential Impact

Privilege escalation may allow the attacker to compromise additional systems or Active Directory itself.

### Relevant Security Objectives

* SEC-OBJ-02
* SEC-OBJ-03
* SEC-OBJ-05
* SEC-OBJ-06
* SEC-OBJ-26

---

## THR-AD-004 — Privileged Account Compromise

### Target

Administrative identities.

### Attack Scenario

An attacker obtains credentials belonging to an administrator.

### Potential Impact

Depending on the account, the attacker may be able to:

* create users;
* modify security groups;
* reset passwords;
* modify GPOs;
* access servers;
* disable security controls;
* create persistence; or
* compromise the domain.

### Relevant Security Objectives

* SEC-OBJ-04
* SEC-OBJ-05
* SEC-OBJ-06
* SEC-OBJ-09
* SEC-OBJ-23

### Security Controls

* separate administrator accounts;
* restricted privileged-group membership;
* restricted administrative logon;
* hardened administrative systems;
* privileged activity logging; and
* least privilege.

---

## THR-AD-005 — Lateral Movement

### Target

Servers, workstations and critical infrastructure.

### Attack Scenario

After compromising one system, an attacker attempts to access additional systems.

Potential mechanisms include:

* stolen credentials;
* administrative shares;
* SMB;
* remote administration;
* Remote Desktop;
* PowerShell remoting;
* reused administrator credentials; and
* unnecessary network connectivity.

### Potential Impact

Lateral movement may expand a small endpoint compromise into a domain-wide incident.

### Relevant Security Objectives

* SEC-OBJ-03
* SEC-OBJ-06
* SEC-OBJ-13
* SEC-OBJ-14
* SEC-OBJ-19
* SEC-OBJ-25

---

## THR-AD-006 — Domain Controller Compromise

### Target

DC01.

### Attack Scenario

An attacker gains administrative control over the Domain Controller directly or through compromised privileged credentials.

### Potential Impact

Potential consequences include:

* domain-wide administrative control;
* credential compromise;
* security-policy manipulation;
* persistence;
* user impersonation;
* security-group manipulation; and
* widespread lateral movement.

### Relevant Security Objectives

* SEC-OBJ-05
* SEC-OBJ-06
* SEC-OBJ-09
* SEC-OBJ-10
* SEC-OBJ-19
* SEC-OBJ-21
* SEC-OBJ-23

### Security Priority

**Critical**

Domain Controller compromise is treated as one of the highest-impact scenarios in the project.

---

## THR-AD-007 — Kerberoasting

### Target

Kerberos service accounts.

### Attack Scenario

An authenticated attacker requests Kerberos service tickets associated with service principal names and attempts offline password recovery against captured ticket material.

### Potential Impact

A weak service-account password may result in service-account compromise and potentially privilege escalation.

### Relevant Security Objectives

* SEC-OBJ-03
* SEC-OBJ-07
* SEC-OBJ-10
* SEC-OBJ-29

### Security Controls

Potential controls include:

* strong service-account credentials;
* minimal service-account privileges;
* managed service accounts where appropriate;
* service-account inventory; and
* monitoring.

---

## THR-AD-008 — Credential Hash Reuse / Pass-the-Hash

### Target

Windows administrative credentials.

### Attack Scenario

An attacker obtains reusable authentication material from a compromised Windows system and attempts to authenticate to other systems.

### Potential Impact

The attack may enable:

* lateral movement;
* server compromise;
* privilege escalation; and
* broader domain compromise.

### Relevant Security Objectives

* SEC-OBJ-03
* SEC-OBJ-04
* SEC-OBJ-06
* SEC-OBJ-19

### Security Controls

Potential controls include:

* reducing administrative logon exposure;
* separate privileged accounts;
* limiting local administrator rights;
* endpoint hardening;
* segmentation; and
* restricted remote administration.

---

## THR-AD-009 — Group Policy Abuse

### Target

Group Policy infrastructure.

### Attack Scenario

An attacker or unauthorised administrator modifies a GPO.

A malicious GPO could potentially:

* weaken security settings;
* disable protections;
* modify firewall rules;
* deploy scripts;
* alter user rights; or
* change configuration across multiple systems.

### Potential Impact

Because Group Policy operates centrally, malicious changes may affect many domain systems simultaneously.

### Relevant Security Objectives

* SEC-OBJ-10
* SEC-OBJ-11
* SEC-OBJ-12
* SEC-OBJ-23
* SEC-OBJ-24

### Security Controls

* restrict GPO modification;
* least privilege;
* privileged administration;
* change auditing;
* GPResult validation; and
* configuration review.

---

## THR-AD-010 — Insecure or Excessive Group Membership

### Target

Active Directory authorisation.

### Attack Scenario

Users receive unnecessary membership in privileged or resource-access groups.

### Potential Impact

Excessive access may allow:

* unauthorised information access;
* privilege escalation;
* administrative actions; or
* increased impact following account compromise.

### Relevant Security Objectives

* SEC-OBJ-02
* SEC-OBJ-03
* SEC-OBJ-05
* SEC-OBJ-26

---

# 10. DNS Threats

## THR-DNS-001 — DNS Misconfiguration or Manipulation

### Target

Active Directory-integrated DNS.

### Attack Scenario

DNS settings or records are incorrectly or maliciously modified.

### Potential Impact

Potential consequences include:

* failed Domain Controller discovery;
* authentication disruption;
* Group Policy failures;
* traffic redirection; and
* service disruption.

### Relevant Security Objectives

* SEC-OBJ-10
* SEC-OBJ-18
* SEC-OBJ-23
* SEC-OBJ-24

### Security Controls

* restricted DNS administration;
* secure DNS configuration;
* controlled dynamic updates;
* configuration review; and
* monitoring.

---

# 11. Endpoint Threats

## THR-END-001 — Corporate Workstation Compromise

### Target

CLIENT01 or equivalent corporate endpoints.

### Attack Scenario

A user endpoint becomes compromised through simulated or conceptual mechanisms such as:

* phishing;
* malicious documents;
* malware;
* vulnerable applications;
* weak configuration; or
* credential compromise.

### Potential Impact

The attacker may gain:

* user credentials;
* access to shared resources;
* information about the domain;
* a foothold for lateral movement; or
* opportunities for privilege escalation.

### Relevant Security Objectives

* SEC-OBJ-03
* SEC-OBJ-19
* SEC-OBJ-20
* SEC-OBJ-21
* SEC-OBJ-24

---

## THR-END-002 — Malicious PowerShell Activity

### Target

Windows endpoints and servers.

### Attack Scenario

An attacker uses PowerShell for:

* discovery;
* execution;
* system modification;
* remote administration; or
* post-compromise activity.

### Potential Impact

PowerShell provides legitimate administrative capabilities that may also be abused by attackers.

### Relevant Security Objectives

* SEC-OBJ-19
* SEC-OBJ-21
* SEC-OBJ-27
* SEC-OBJ-28

### Security Controls

Potential controls include:

* PowerShell logging;
* script-block logging;
* module logging where appropriate;
* secure execution policies as a management control;
* endpoint protection; and
* monitoring.

---

# 12. Network Threats

## THR-NET-001 — Unrestricted Inter-Zone Communication

### Target

Network security boundaries.

### Attack Scenario

Overly permissive firewall rules allow unnecessary communication between security zones.

### Potential Impact

A compromised system may be able to reach:

* OT systems;
* backup systems; or
* other critical infrastructure.

### Relevant Security Objectives

* SEC-OBJ-13
* SEC-OBJ-14
* SEC-OBJ-25

### Security Controls

* pfSense firewall;
* deny-by-default principles;
* explicit allow rules;
* rule documentation;
* firewall logging; and
* connectivity testing.

---

## THR-NET-002 — Firewall Misconfiguration

### Target

PFSENSE01.

### Attack Scenario

A firewall rule is configured incorrectly and unintentionally exposes systems or services.

### Potential Impact

Security-zone isolation may be weakened or bypassed.

### Relevant Security Objectives

* SEC-OBJ-13
* SEC-OBJ-14
* SEC-OBJ-24
* SEC-OBJ-25
* SEC-OBJ-29

### Security Controls

* firewall rule review;
* configuration documentation;
* least-privilege rules;
* rule testing;
* logging; and
* change validation.

---

# 13. Manufacturing / OT Threats

## THR-OT-001 — Corporate-to-OT Lateral Movement

### Target

Manufacturing / OT zone.

### Attack Scenario

An attacker compromises a corporate endpoint and attempts to reach OT01 or other manufacturing systems.

```text
Corporate User
      ↓
   CLIENT01
      ↓
Endpoint Compromise
      ↓
Lateral Movement
      ↓
   PFSENSE01
      ↓
     OT01
```

### Desired Security Outcome

pfSense should prevent communication that has not been explicitly authorised.

```text
CLIENT01
    |
    | Unauthorised traffic
    v
PFSENSE01
    |
    X  BLOCK
    |
   OT01
```

### Potential Impact

Successful compromise of manufacturing systems could affect:

* production availability;
* manufacturing operations;
* business continuity;
* product delivery; and
* potentially safety in a real industrial environment.

### Relevant Security Objectives

* SEC-OBJ-13
* SEC-OBJ-14
* SEC-OBJ-15
* SEC-OBJ-25

### Security Priority

**Critical**

---

# 14. Backup Threats

## THR-BAK-001 — Backup Infrastructure Compromise

### Target

BACKUP01 and backup infrastructure.

### Attack Scenario

An attacker who has compromised corporate systems attempts to access the Backup zone.

```text
CLIENT01
    ↓
Compromise
    ↓
Privilege Escalation
    ↓
PFSENSE01
    ↓
BACKUP01
```

### Potential Attacker Objectives

* delete backups;
* encrypt backups;
* modify backup data;
* steal backup data; or
* disable recovery services.

### Potential Impact

The organisation may be unable to recover following a ransomware or destructive cyber incident.

### Relevant Security Objectives

* SEC-OBJ-13
* SEC-OBJ-14
* SEC-OBJ-16
* SEC-OBJ-17
* SEC-OBJ-25

### Security Priority

**Critical**

---

# 15. Logging Threats

## THR-LOG-001 — Insufficient Security Logging

### Target

Security monitoring capability.

### Attack Scenario

Important security activity occurs without sufficient audit records.

### Potential Impact

BlueWave may be unable to:

* identify attacks;
* investigate incidents;
* attribute administrative actions;
* validate controls; or
* reconstruct attacker activity.

### Relevant Security Objectives

* SEC-OBJ-21
* SEC-OBJ-22
* SEC-OBJ-23
* SEC-OBJ-32

---

## THR-LOG-002 — Log Deletion or Tampering

### Target

Windows and firewall security logs.

### Attack Scenario

An attacker with sufficient privilege clears or modifies logs to reduce evidence of malicious activity.

### Potential Impact

Incident investigation and detection capability may be degraded.

### Relevant Security Objectives

* SEC-OBJ-21
* SEC-OBJ-23
* SEC-OBJ-32

---

# 16. Ransomware Attack Scenario

One of the project's primary end-to-end threat scenarios is a ransomware-style intrusion.

## Stage 1 — Initial Access

```text
Phishing / Stolen Credential
             ↓
        Corporate User
             ↓
          CLIENT01
```

Potential controls:

* secure authentication;
* endpoint hardening;
* least privilege;
* patching; and
* security logging.

---

## Stage 2 — Discovery

The attacker attempts to identify:

* domain information;
* users;
* groups;
* servers;
* privileged accounts; and
* accessible resources.

Potential controls:

* least privilege;
* endpoint hardening;
* monitoring; and
* reduced unnecessary access.

---

## Stage 3 — Credential Access

```text
CLIENT01
    ↓
Credential Theft
    ↓
Additional Identity
```

Potential controls:

* account separation;
* reduced administrative logon;
* endpoint hardening; and
* privileged-access restrictions.

---

## Stage 4 — Privilege Escalation

```text
Standard User
     ↓
Privilege Escalation
     ↓
Administrator
```

Potential controls:

* least privilege;
* restricted administrative groups;
* secure configuration;
* patching; and
* privileged access controls.

---

## Stage 5 — Active Directory Compromise

```text
Administrator
     ↓
   DC01
     ↓
Domain Control
```

Potential controls:

* Domain Controller hardening;
* separate privileged accounts;
* restricted administrative access;
* Group Policy security;
* auditing; and
* monitoring.

---

## Stage 6 — Lateral Movement

```text
          Domain Compromise
                 |
        +--------+--------+
        |                 |
        v                 v
    FILESERVER01       Other Systems
```

Potential controls:

* network restrictions;
* endpoint firewall;
* least privilege;
* credential protection; and
* monitoring.

---

## Stage 7 — Attempted OT Compromise

```text
Corporate Compromise
        ↓
    PFSENSE01
        ↓
       BLOCK
        ↓
       OT01
```

Primary controls:

* network segmentation;
* pfSense;
* restrictive firewall policy; and
* firewall testing.

---

## Stage 8 — Attempted Backup Compromise

```text
Corporate Compromise
        ↓
    PFSENSE01
        ↓
       BLOCK
        ↓
    BACKUP01
```

Primary controls:

* dedicated Backup zone;
* restrictive firewall policy;
* least-privilege administration; and
* backup isolation.

---

## Stage 9 — Ransomware Deployment

If the attacker cannot compromise OT or backup infrastructure, segmentation may reduce the incident's blast radius and preserve recovery capability.

This demonstrates the value of **defence in depth**.

No single security control is expected to prevent every stage of the attack.

---

# 17. Attack Path Analysis

The primary high-risk attack path considered by the project is:

```text
External Threat
      ↓
Credential Theft / Endpoint Compromise
      ↓
Corporate User Account
      ↓
CLIENT01
      ↓
Credential Discovery
      ↓
Privilege Escalation
      ↓
Privileged Account
      ↓
Active Directory
      ↓
DC01 Compromise
      ↓
Domain-Wide Control
      |
      +------------------+
      |                  |
      v                  v
OT Compromise       Backup Compromise
      |                  |
      v                  v
Production Impact   Recovery Failure
```

The security architecture should attempt to break this attack chain at multiple points.

---

# 18. Defence-in-Depth Mapping

| Attack Stage                  | Preventative Controls                     | Detective Controls          | Recovery / Limiting Controls     |
| ----------------------------- | ----------------------------------------- | --------------------------- | -------------------------------- |
| Credential Attack             | Password controls, account separation     | Failed-logon monitoring     | Password reset / account disable |
| Endpoint Compromise           | Hardening, Defender, patching             | Event logging               | System rebuild                   |
| Privilege Escalation          | Least privilege, restricted admin rights  | Privilege-change auditing   | Remove unauthorised privilege    |
| Privileged Account Compromise | Separate admin accounts, restricted use   | Privileged logon monitoring | Credential rotation              |
| AD Attack                     | DC hardening, restricted administration   | AD auditing                 | Recovery procedures              |
| Lateral Movement              | Segmentation, firewall, endpoint controls | Firewall and Windows logs   | System isolation                 |
| OT Access                     | Dedicated OT zone, firewall rules         | pfSense logs                | OT isolation                     |
| Backup Access                 | Dedicated Backup zone, restricted access  | Firewall and system logs    | Protected backups                |
| Ransomware                    | Multiple preventative layers              | Endpoint/security logs      | Backup and recovery              |

---

# 19. Threat Priority Summary

The following initial qualitative priority is used during planning.

| Threat ID   | Threat                                    | Initial Priority |
| ----------- | ----------------------------------------- | ---------------- |
| THR-AD-001  | Credential Theft                          | High             |
| THR-AD-002  | Password Spraying / Authentication Attack | High             |
| THR-AD-003  | Privilege Escalation                      | High             |
| THR-AD-004  | Privileged Account Compromise             | Critical         |
| THR-AD-005  | Lateral Movement                          | High             |
| THR-AD-006  | Domain Controller Compromise              | Critical         |
| THR-AD-007  | Kerberoasting                             | High             |
| THR-AD-008  | Credential Hash Reuse / Pass-the-Hash     | High             |
| THR-AD-009  | Group Policy Abuse                        | High             |
| THR-AD-010  | Excessive Group Membership                | High             |
| THR-DNS-001 | DNS Misconfiguration / Manipulation       | High             |
| THR-END-001 | Corporate Workstation Compromise          | High             |
| THR-END-002 | Malicious PowerShell Activity             | Medium / High    |
| THR-NET-001 | Unrestricted Inter-Zone Communication     | Critical         |
| THR-NET-002 | Firewall Misconfiguration                 | High             |
| THR-OT-001  | Corporate-to-OT Lateral Movement          | Critical         |
| THR-BAK-001 | Backup Infrastructure Compromise          | Critical         |
| THR-LOG-001 | Insufficient Security Logging             | High             |
| THR-LOG-002 | Log Deletion / Tampering                  | High             |

These ratings are preliminary threat priorities rather than formal risk ratings.

Formal likelihood, impact, inherent risk, existing controls, treatment, and residual risk will be assessed during the Risk Assessment phase.

---

# 20. Threat-to-Security-Objective Traceability

| Threat                        | Primary Security Objectives        |
| ----------------------------- | ---------------------------------- |
| Credential Theft              | SEC-OBJ-03, 04, 06, 07, 21         |
| Password Spraying             | SEC-OBJ-07, 08, 21, 22             |
| Privilege Escalation          | SEC-OBJ-02, 03, 05, 06, 26         |
| Privileged Account Compromise | SEC-OBJ-04, 05, 06, 09, 23         |
| Lateral Movement              | SEC-OBJ-03, 06, 13, 14, 19, 25     |
| Domain Controller Compromise  | SEC-OBJ-05, 06, 09, 10, 19, 21, 23 |
| Kerberoasting                 | SEC-OBJ-03, 07, 10, 29             |
| Hash Reuse                    | SEC-OBJ-03, 04, 06, 19             |
| Group Policy Abuse            | SEC-OBJ-10, 11, 12, 23, 24         |
| Excessive Group Membership    | SEC-OBJ-02, 03, 05, 26             |
| DNS Manipulation              | SEC-OBJ-10, 18, 23, 24             |
| Endpoint Compromise           | SEC-OBJ-03, 19, 20, 21, 24         |
| Malicious PowerShell          | SEC-OBJ-19, 21, 27, 28             |
| Unrestricted Network Access   | SEC-OBJ-13, 14, 25                 |
| Firewall Misconfiguration     | SEC-OBJ-13, 14, 24, 25, 29         |
| Corporate-to-OT Movement      | SEC-OBJ-13, 14, 15, 25             |
| Backup Compromise             | SEC-OBJ-13, 14, 16, 17, 25         |
| Insufficient Logging          | SEC-OBJ-21, 22, 23, 32             |
| Log Tampering                 | SEC-OBJ-21, 23, 32                 |

---

# 21. Example Future Control Traceability

As later phases are completed, threat records will become linked to specific controls and evidence.

Example:

```text
THR-AD-004
Privileged Account Compromise
        ↓
RISK-AD-004
        ↓
SEC-OBJ-04
Separate Administrative Accounts
        ↓
CTRL-PAM-001
Separate Privileged Identity
        ↓
Implementation
adm.<username>
        ↓
TEST-PAM-001
Verify Standard Account Has No
Domain Administrative Privileges
        ↓
EVID-PAM-001
AD Group Membership Evidence
        ↓
Framework Mapping
ISO / NIST / Essential Eight
        ↓
Residual Risk
```

This traceability will be developed progressively rather than attempting to complete all control mappings during the planning phase.

---

# 22. Security Testing Boundary

All security testing conducted for this project must remain within the authorised lab environment.

Testing should:

* target only lab-owned systems;
* avoid external organisations;
* avoid unauthorised systems;
* use controlled test accounts where appropriate;
* preserve evidence;
* document expected outcomes; and
* avoid unnecessary destructive actions.

The purpose of security testing is to validate defensive controls rather than to attack third-party infrastructure.

---

# 23. Threat Model Limitations

This threat model represents a simplified educational environment and is not intended to represent every possible Active Directory attack technique.

Limitations include:

* simplified network architecture;
* limited number of virtual machines;
* no production Entra ID integration;
* simulated OT systems;
* simulated backup infrastructure;
* limited enterprise monitoring tooling;
* limited high-availability infrastructure; and
* home-lab resource constraints.

Threats will be added or modified as new attack surfaces are introduced.

---

# 24. Threat Model Review Triggers

The threat model should be reviewed when:

* a new system is added;
* a new security zone is introduced;
* firewall architecture changes;
* privileged-access architecture changes;
* new remote-management services are enabled;
* new service accounts are introduced;
* significant GPOs are created;
* new monitoring capabilities are deployed;
* security testing identifies a new attack path; or
* a previously documented assumption changes.

---

# 25. Key Security Assumption

The project assumes that compromise may occur.

The security objective is therefore not limited to preventing initial access.

The architecture should also:

* restrict attacker privileges;
* reduce credential exposure;
* limit lateral movement;
* protect critical infrastructure;
* detect suspicious behaviour;
* preserve evidence; and
* maintain recovery capability.

This represents an **assume-breach and defence-in-depth approach** to Active Directory security.

---

# 26. Expected Threat-Model Outcome

The threat model should allow each major technical security decision to answer:

> **Which threat or attack path does this control reduce?**

For example:

```text
Separate Admin Accounts
        ↓
Reduces privileged credential exposure
        ↓
THR-AD-004
```

```text
pfSense OT Firewall Rules
        ↓
Restricts corporate-to-OT movement
        ↓
THR-OT-001
```

```text
Backup Zone Isolation
        ↓
Protects recovery infrastructure
        ↓
THR-BAK-001
```

```text
PowerShell Logging
        ↓
Improves visibility of PowerShell activity
        ↓
THR-END-002
```

This ensures that security controls are implemented because they address identifiable security risks rather than simply because they appear on a hardening checklist.

---

# 27. Document Status

| Field                    | Value                                                         |
| ------------------------ | ------------------------------------------------------------- |
| Project                  | Active-Directory-Cybersecurity-Lab                            |
| Document                 | Threat Model                                                  |
| Organisation             | BlueWave Manufacturing Pty Ltd                                |
| Environment              | Authorised Cybersecurity Home Lab                             |
| Primary Asset            | Microsoft Active Directory                                    |
| Primary Security Gateway | PFSENSE01                                                     |
| Primary Threat Scenario  | Identity Compromise → Privilege Escalation → Lateral Movement |
| Critical Protected Zones | Manufacturing / OT and Backup                                 |
| Threats Identified       | 19                                                            |
| Method                   | Practical Risk-Based Threat Modelling                         |
| Status                   | Initial Threat Model Defined                                  |
