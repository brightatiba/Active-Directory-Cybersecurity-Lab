# Active Directory Cybersecurity Lab — Network Security Architecture

## 1. Document Purpose

This document defines the network security architecture for the **Active-Directory-Cybersecurity-Lab** based on the fictional BlueWave Manufacturing Pty Ltd environment.

The purpose of the document is to describe:

* the virtual network architecture;
* security zones;
* network addressing;
* pfSense interfaces;
* gateways;
* DHCP and static-addressing requirements;
* system placement;
* network trust levels;
* routing boundaries;
* DNS dependencies;
* administrative communication paths;
* inter-zone communication principles;
* firewall enforcement responsibilities;
* network-security risks;
* validation requirements; and
* production-state recommendations.

The architecture supports the broader security objective:

> A compromise in one part of the BlueWave environment should not automatically provide unrestricted access to other critical systems or security zones.

---

# 2. Network Security Objectives

The network architecture is designed to support the following objectives:

* **SEC-OBJ-13** — Enforce Network Segmentation
* **SEC-OBJ-14** — Apply Least-Privilege Network Access
* **SEC-OBJ-15** — Protect Manufacturing Systems from Corporate Compromise
* **SEC-OBJ-16** — Isolate Backup Infrastructure
* **SEC-OBJ-17** — Preserve Recovery Capability
* **SEC-OBJ-18** — Protect Internal DNS Services
* **SEC-OBJ-25** — Test Network Isolation

The design also supports the broader principles of:

* defence in depth;
* least privilege;
* assume breach;
* secure-by-default configuration; and
* controlled trust boundaries.

---

# 3. Architecture Constraint

The BlueWave lab is hosted using **Oracle VirtualBox**.

Because of practical virtual network-adapter limitations and home-lab resource constraints, the network has been simplified into three major internal security zones rather than implementing a separate VLAN or network for every enterprise function.

The implemented design consists of:

1. **Corporate / Management Zone**
2. **Manufacturing / OT Zone**
3. **Backup Zone**

pfSense provides routing and security enforcement between these zones.

This design is appropriate for the lab but is not presented as the preferred production architecture.

---

# 4. High-Level Network Architecture

```text
                         HOME NETWORK
                              |
                              |
                         pfSense WAN
                              |
                       +-------------+
                       |  PFSENSE01  |
                       | Firewall /  |
                       | Router      |
                       +------+------+
                              |
             +----------------+----------------+
             |                |                |
             |                |                |
             v                v                v

      CORPORATE / MGMT      OT /            BACKUP
           ZONE         MANUFACTURING         ZONE

      192.168.10.0/24    192.168.20.0/24   192.168.30.0/24

             |                |                |
             |                |                |
     +-------+------+        OT01          BACKUP01
     |       |      |
    DC01  FILESERVER01
             |
          CLIENT01
             |
          ADMIN01
```

All communication between the internal security zones must pass through **PFSENSE01**.

---

# 5. Network Addressing Plan

The lab uses separate `/24` networks for each internal security zone.

| Zone                   | Network           | Gateway        | Security Purpose                         |
| ---------------------- | ----------------- | -------------- | ---------------------------------------- |
| Corporate / Management | `192.168.10.0/24` | `192.168.10.1` | AD, servers, users and administration    |
| Manufacturing / OT     | `192.168.20.0/24` | `192.168.20.1` | Simulated manufacturing infrastructure   |
| Backup                 | `192.168.30.0/24` | `192.168.30.1` | Protected backup/recovery infrastructure |

The use of separate IP networks provides clear routing and firewall boundaries.

---

# 6. PFSENSE01 Interface Architecture

PFSENSE01 provides one external/WAN connection and separate interfaces for the internal security zones.

| Interface | Network           | Address                   | Function                         |
| --------- | ----------------- | ------------------------- | -------------------------------- |
| WAN       | Home network      | DHCP or upstream-assigned | Internet / external connectivity |
| CORP      | `192.168.10.0/24` | `192.168.10.1/24`         | Corporate / Management gateway   |
| OT        | `192.168.20.0/24` | `192.168.20.1/24`         | Manufacturing / OT gateway       |
| BACKUP    | `192.168.30.0/24` | `192.168.30.1/24`         | Backup-zone gateway              |

PFSENSE01 therefore acts as the primary Layer 3 security enforcement point for communication between security zones.

---

# 7. Corporate / Management Zone

## 7.1 Network

```text
192.168.10.0/24
```

## 7.2 Default Gateway

```text
192.168.10.1
```

## 7.3 Purpose

This zone contains systems representing:

* IT / Management;
* Active Directory infrastructure;
* Windows servers;
* corporate users;
* administrative systems; and
* guest/test systems where required by the lab.

## 7.4 Example Systems

```text
DC01
FILESERVER01
CLIENT01
ADMIN01
```

## 7.5 Security Classification

**Trust Level: Medium to High**

The zone contains both ordinary corporate endpoints and highly sensitive systems.

Because systems with different trust levels share the same network, host-based controls are particularly important.

---

# 8. Corporate Zone Security Risk

The combined Corporate / Management zone creates a known limitation.

A compromised corporate workstation may have direct Layer 2/Layer 3 reachability to systems such as:

* DC01;
* FILESERVER01; and
* ADMIN01.

This can increase opportunities for:

* network discovery;
* lateral movement;
* SMB attacks;
* credential attacks;
* administrative service discovery; and
* exploitation of exposed services.

The network architecture therefore relies on additional controls including:

* Windows Firewall;
* Group Policy;
* least privilege;
* separate privileged accounts;
* restricted remote administration;
* endpoint hardening;
* server hardening; and
* logging.

---

# 9. Corporate Zone DHCP

The Corporate / Management network uses pfSense DHCP for appropriate client systems.

The existing DHCP design is:

```text
Network:
192.168.10.0/24

Gateway:
192.168.10.1

DHCP Range:
192.168.10.100 - 192.168.10.200
```

Dynamic addressing is suitable for ordinary test/client systems.

Security-sensitive infrastructure should use static addressing or DHCP reservations.

---

# 10. Corporate Zone Static Infrastructure

Infrastructure systems should use predictable IP addresses.

A recommended lab allocation is:

| System         | Proposed Address | Addressing         |
| -------------- | ---------------- | ------------------ |
| PFSENSE01 CORP | `192.168.10.1`   | Static             |
| DC01           | `192.168.10.10`  | Static             |
| FILESERVER01   | `192.168.10.20`  | Static             |
| ADMIN01        | `192.168.10.30`  | Static             |
| CLIENT01       | DHCP or reserved | DHCP / Reservation |

Addresses may be adjusted where existing lab systems already use established IP addresses.

The security principle is more important than the exact numbering:

> Critical infrastructure should not depend on unpredictable client addressing.

---

# 11. Manufacturing / OT Zone

## 11.1 Network

```text
192.168.20.0/24
```

## 11.2 Default Gateway

```text
192.168.20.1
```

## 11.3 Purpose

The OT zone represents BlueWave's manufacturing infrastructure.

Example system:

```text
OT01
```

The environment is simulated and does not contain real industrial control systems.

## 11.4 Security Classification

**Trust Level: Restricted / Critical**

The OT zone represents systems where compromise may affect:

* production;
* availability;
* manufacturing operations;
* equipment;
* product delivery; and
* safety in a real industrial environment.

---

# 12. OT Network Security Principle

The Corporate network should not have unrestricted access to OT.

The desired architecture is:

```text
Corporate System
      |
      |
      v
  PFSENSE01
      |
      | Firewall Policy
      v
     DENY
      |
      X
      |
    OT01
```

Only specifically authorised traffic should cross the boundary.

---

# 13. OT Addressing

A recommended addressing model is:

| System       | Address         |
| ------------ | --------------- |
| PFSENSE01 OT | `192.168.20.1`  |
| OT01         | `192.168.20.10` |

OT systems should generally use predictable addresses to support:

* firewall rules;
* logging;
* asset identification;
* troubleshooting; and
* access control.

---

# 14. OT DHCP Strategy

For this lab, OT systems should preferably use:

* static IP addressing; or
* tightly controlled DHCP reservations.

This reflects the principle that infrastructure and manufacturing systems should have predictable network identities.

A production OT environment may have additional requirements depending on vendor and industrial protocol constraints.

---

# 15. Backup Zone

## 15.1 Network

```text
192.168.30.0/24
```

## 15.2 Default Gateway

```text
192.168.30.1
```

## 15.3 Purpose

The Backup zone represents protected backup and recovery infrastructure.

Example system:

```text
BACKUP01
```

## 15.4 Security Classification

**Trust Level: Restricted / Critical**

Backup infrastructure is treated as a high-value security target.

---

# 16. Backup Network Security Principle

A compromise of the Corporate network must not automatically expose backup infrastructure.

The desired architecture is:

```text
Compromised Corporate Host
           |
           v
       PFSENSE01
           |
           | Restrictive Policy
           v
          DENY
           |
           X
           |
        BACKUP01
```

Only explicitly required backup and administrative communication should be allowed.

---

# 17. Backup Addressing

A recommended addressing model is:

| System           | Address         |
| ---------------- | --------------- |
| PFSENSE01 BACKUP | `192.168.30.1`  |
| BACKUP01         | `192.168.30.10` |

BACKUP01 should use a static or otherwise predictable IP address.

---

# 18. Why Backup Requires Separate Segmentation

Backup segmentation is intended to reduce the effectiveness of ransomware and destructive attacks.

A common high-impact attack path is:

```text
Corporate Endpoint
       ↓
Credential Theft
       ↓
Privilege Escalation
       ↓
Lateral Movement
       ↓
Backup Discovery
       ↓
Backup Destruction
       ↓
Ransomware Deployment
```

The Backup zone introduces an additional barrier before recovery infrastructure can be reached.

---

# 19. VirtualBox Network Architecture

Oracle VirtualBox provides the virtual network infrastructure connecting systems to PFSENSE01.

The logical design should resemble:

```text
VirtualBox Host
│
├── WAN / Home Network Adapter
│      │
│      └── PFSENSE01 WAN
│
├── Corporate Internal Network
│      │
│      ├── PFSENSE01 CORP
│      ├── DC01
│      ├── FILESERVER01
│      ├── CLIENT01
│      └── ADMIN01
│
├── OT Internal Network
│      │
│      ├── PFSENSE01 OT
│      └── OT01
│
└── Backup Internal Network
       │
       ├── PFSENSE01 BACKUP
       └── BACKUP01
```

The exact VirtualBox network names should be documented consistently.

Recommended descriptive names include:

```text
BW-CORP
BW-OT
BW-BACKUP
```

or equivalent names matching the existing lab configuration.

---

# 20. Routing Architecture

PFSENSE01 is responsible for Layer 3 routing between internal networks.

No other lab system should act as an unauthorised router between zones.

The intended routing model is:

```text
192.168.10.0/24
        |
        |
    192.168.10.1
        |
   +----+----+
   | PFSENSE |
   +----+----+
        |
   +----+------------------+
   |                       |
192.168.20.1            192.168.30.1
   |                       |
OT Zone                Backup Zone
```

This ensures that traffic crossing security zones can be inspected and controlled.

---

# 21. Inter-Zone Security Policy

Inter-zone communication should follow the principle:

> **No communication should be permitted solely because routing exists.**

Routing enables connectivity.

Firewall policy determines whether that connectivity is authorised.

Therefore:

```text
Route Exists
     ≠
Access Allowed
```

---

# 22. Default Inter-Zone Philosophy

The long-term firewall philosophy is:

```text
DEFAULT DENY
     +
EXPLICIT ALLOW
```

This means that traffic crossing trust boundaries should be blocked unless a documented business or technical requirement exists.

---

# 23. Initial Zone Communication Model

At a high level, the intended model is:

| Source    | Destination | Default Position                         |
| --------- | ----------- | ---------------------------------------- |
| Corporate | Internet    | Allow as required                        |
| Corporate | Corporate   | Governed primarily by host controls      |
| Corporate | OT          | Deny unless specifically required        |
| Corporate | Backup      | Deny unless specifically required        |
| OT        | Corporate   | Deny unless specifically required        |
| OT        | Backup      | Deny                                     |
| Backup    | Corporate   | Deny unless required for backup/recovery |
| Backup    | OT          | Deny unless specifically required        |
| OT        | Internet    | Restricted / deny unless required        |
| Backup    | Internet    | Restricted / allow only where required   |

The exact allowed services will be documented later in the **Security Traffic Flow Matrix**.

---

# 24. East-West vs North-South Traffic

The architecture considers two broad traffic types.

## North-South Traffic

Traffic entering or leaving the lab or a security zone.

Example:

```text
CLIENT01
   ↓
PFSENSE01
   ↓
Internet
```

or:

```text
Corporate
   ↓
PFSENSE01
   ↓
OT
```

## East-West Traffic

Traffic between systems within the same trust zone.

Example:

```text
CLIENT01
   ↓
DC01
```

Because CLIENT01 and DC01 currently share the Corporate / Management zone, pfSense may not inspect all traffic between them.

Host-based protections therefore become important.

---

# 25. Host Firewall Architecture

Windows Firewall provides an additional layer of network security.

It should be used to restrict unnecessary host communication even within the same security zone.

For example:

```text
CLIENT01
    |
    | Same Subnet
    v
   DC01
```

The absence of a pfSense boundary does not mean that every service on DC01 should be accessible from CLIENT01.

Windows Firewall should restrict access to required services.

---

# 26. Active Directory Network Dependency

Domain clients require communication with Active Directory infrastructure.

This can involve services such as:

* DNS;
* Kerberos;
* LDAP;
* SMB;
* RPC;
* Global Catalog where applicable;
* time synchronisation; and
* Group Policy-related communication.

These requirements will later be documented carefully in the traffic-flow matrix rather than allowing unrestricted access without justification.

---

# 27. DNS Architecture

DC01 provides DNS services for the Active Directory domain.

Domain-joined systems should use the Active Directory DNS infrastructure for internal domain resolution.

The desired model is:

```text
CLIENT01
   |
   | DNS Query
   v
  DC01
   |
   | Forward External Query
   v
Approved Upstream Resolver
```

Clients should not bypass AD DNS for domain-related name resolution.

---

# 28. DNS Security Principle

Active Directory depends heavily on DNS.

Incorrect client DNS configuration can cause failures involving:

* domain join;
* authentication;
* Group Policy;
* Domain Controller discovery;
* Kerberos; and
* internal resource access.

DNS should therefore be treated as part of the Active Directory security architecture rather than merely a convenience service.

---

# 29. DHCP Architecture

pfSense provides DHCP where appropriate.

The design separates infrastructure and endpoint addressing.

## Infrastructure

Prefer:

* static addresses; or
* DHCP reservations.

Examples:

* DC01
* FILESERVER01
* ADMIN01
* OT01
* BACKUP01

## User/Test Endpoints

May use dynamic DHCP.

Example:

* CLIENT01

---

# 30. DHCP Security Considerations

DHCP configuration should define appropriate:

* default gateway;
* DNS server;
* address range;
* subnet mask; and
* lease settings.

For domain-joined Corporate clients, DHCP should direct DNS queries toward the Active Directory DNS server where appropriate.

For example:

```text
CLIENT01 DHCP
     |
     +---- IP Address
     |
     +---- Gateway: 192.168.10.1
     |
     +---- DNS: DC01
```

---

# 31. Internet Access Architecture

Internet access should pass through PFSENSE01.

```text
Internal System
      |
      v
PFSENSE01
      |
      v
Home Network / Internet
```

Internet access should not create a path that bypasses security-zone controls.

---

# 32. Management Architecture

Security-sensitive infrastructure must have controlled management paths.

Examples include:

* PFSENSE01 administration;
* DC01 administration;
* FILESERVER01 administration;
* BACKUP01 administration;
* OT administrative access.

Administrative access should originate from authorised administrative identities and, where possible, the designated administrative system.

Example:

```text
Administrator
      |
      v
   ADMIN01
      |
      v
Approved Administrative Service
      |
      v
Target Infrastructure
```

---

# 33. pfSense Administration

Access to the pfSense management interface should be restricted.

The management interface should not be unnecessarily accessible from:

* OT systems;
* Backup systems;
* guest systems; or
* untrusted endpoints.

Administrative access should ideally originate only from approved systems in the Corporate / Management zone.

---

# 34. Domain Controller Administration

DC01 should not be administered casually from ordinary user endpoints.

The preferred model is:

```text
Administrator
      |
      v
Privileged Account
      |
      v
   ADMIN01
      |
      v
     DC01
```

This reduces privileged credential exposure.

---

# 35. Remote Administration

Remote-management services should be enabled only where required.

Potential administrative technologies may include:

* Remote Desktop;
* PowerShell Remoting;
* Server Manager;
* MMC consoles;
* Windows Admin Center if later deployed; and
* pfSense web administration.

Each remote-management path increases attack surface and should have a documented requirement.

---

# 36. Network Trust Model

The architecture should not treat all zones as equally trusted.

A simplified trust model is:

| Zone                   | Trust Classification               |
| ---------------------- | ---------------------------------- |
| Corporate / Management | Mixed Trust                        |
| Manufacturing / OT     | Restricted / Critical              |
| Backup                 | Restricted / Critical              |
| Home / External        | Untrusted relative to internal lab |

The Corporate zone is described as **Mixed Trust** because it contains both:

* ordinary user endpoints; and
* privileged infrastructure.

This is a known lab limitation.

---

# 37. Trust Boundary — Corporate to OT

Identifier:

```text
TB-02
```

Threat:

```text
THR-OT-001
Corporate-to-OT Lateral Movement
```

Security objective:

```text
SEC-OBJ-15
Protect Manufacturing Systems from Corporate Compromise
```

Primary enforcement:

```text
PFSENSE01
```

---

# 38. Trust Boundary — Corporate to Backup

Identifier:

```text
TB-03
```

Threat:

```text
THR-BAK-001
Backup Infrastructure Compromise
```

Security objectives:

```text
SEC-OBJ-16
Isolate Backup Infrastructure

SEC-OBJ-17
Preserve Recovery Capability
```

Primary enforcement:

```text
PFSENSE01
```

---

# 39. Network Threat Mapping

| Threat ID   | Threat                                | Network Response                               |
| ----------- | ------------------------------------- | ---------------------------------------------- |
| THR-AD-005  | Lateral Movement                      | Host firewalls + segmentation                  |
| THR-NET-001 | Unrestricted Inter-Zone Communication | pfSense default-deny policy                    |
| THR-NET-002 | Firewall Misconfiguration             | Rule review + testing                          |
| THR-OT-001  | Corporate-to-OT Movement              | OT security zone                               |
| THR-BAK-001 | Backup Compromise                     | Backup security zone                           |
| THR-END-001 | Workstation Compromise                | Limit subsequent network reach                 |
| THR-AD-006  | Domain Controller Compromise          | Host firewall + privileged-access restrictions |

---

# 40. Network Control Architecture

The network security architecture relies on multiple layers.

```text
VirtualBox Network Isolation
          ↓
      PFSENSE01
          ↓
  Inter-Zone Firewall
          ↓
      Host Firewall
          ↓
 Identity / Permissions
          ↓
Logging / Monitoring
```

This avoids depending entirely on one firewall.

---

# 41. Firewall Rule Design Standard

Each significant firewall rule should eventually document:

| Field                | Description                        |
| -------------------- | ---------------------------------- |
| Rule ID              | Unique identifier                  |
| Interface            | pfSense interface                  |
| Source               | Originating network/system         |
| Destination          | Target network/system              |
| Protocol             | TCP/UDP/ICMP/etc.                  |
| Destination Port     | Required service                   |
| Action               | Allow / Deny                       |
| Logging              | Whether events are logged          |
| Business Requirement | Why communication is necessary     |
| Security Rationale   | Why allowing/blocking reduces risk |
| Related Threat       | Threat ID                          |
| Security Objective   | SEC-OBJ ID                         |
| Test                 | Validation procedure               |
| Evidence             | Evidence reference                 |

---

# 42. Example Firewall Rule Logic

An example control could be:

```text
Rule ID:
FW-OT-001

Source:
Corporate Network

Destination:
OT Network

Protocol:
Any

Action:
Deny

Reason:
Corporate endpoints must not have unrestricted access to OT.

Threat:
THR-OT-001

Objective:
SEC-OBJ-15
```

A later explicit allow rule may override this for a specific authorised administrative path.

---

# 43. Rule Ordering

pfSense firewall rules are evaluated according to rule-processing behaviour.

Therefore, rule order must be carefully reviewed.

A broad allow rule should not unintentionally override the intended restrictive architecture.

Rules should be:

* specific;
* documented;
* reviewed; and
* tested.

---

# 44. Network Validation Strategy

Network controls must be tested.

Testing should include both:

## Negative Testing

Prove prohibited communication fails.

Example:

```text
CLIENT01
    |
    | Attempt prohibited connection
    v
PFSENSE01
    |
    X
    |
   OT01
```

## Positive Testing

Prove explicitly authorised communication continues to work.

Example:

```text
Approved Admin Path
       |
       v
   PFSENSE01
       |
     ALLOW
       |
       v
     Target
```

Both are required to demonstrate effective firewall configuration.

---

# 45. Useful Network Validation Commands

Depending on the test, tools may include:

```powershell
Test-NetConnection <destination> -Port <port>
```

For example:

```powershell
Test-NetConnection 192.168.20.10 -Port 445
```

Basic connectivity may also be tested using:

```powershell
ping <destination>
```

However, ping alone should not be treated as proof that an application service is accessible or inaccessible.

Port-specific testing provides stronger evidence.

---

# 46. Firewall Evidence

Strong firewall evidence should combine:

```text
Firewall Rule
      +
Source/Destination Test
      +
pfSense Log
      =
Validated Network Control
```

For example:

```text
FW-OT-001 screenshot
       +
CLIENT01 failed connection to OT01
       +
pfSense block-log entry
       =
Evidence that Corporate → OT access is restricted
```

---

# 47. Network Security Logging

PFSENSE01 should log important blocked traffic where useful for validation and monitoring.

Logs can support:

* firewall troubleshooting;
* control testing;
* attack-path analysis;
* incident investigation; and
* evidence collection.

Not every routine network event needs to be retained indefinitely in the home lab.

Priority should be given to events relevant to security objectives and tests.

---
<!--
# 48. Network Diagram Evidence

The repository should eventually include a network-security diagram showing:

* Home Network;
* PFSENSE01;
* WAN;
* Corporate / Management zone;
* OT zone;
* Backup zone;
* subnets;
* gateways;
* major virtual machines; and
* security boundaries.

The diagram should visually distinguish trust zones rather than merely showing device connections.
-->
---

# 49. Architecture IDs

The following architecture IDs apply to this design.

| ID           | Component                          |
| ------------ | ---------------------------------- |
| ARCH-NET-001 | PFSENSE01 Security Gateway         |
| ARCH-NET-002 | Corporate / Management Zone        |
| ARCH-NET-003 | Manufacturing / OT Zone            |
| ARCH-NET-004 | Backup Zone                        |
| ARCH-NET-005 | Corporate-to-OT Trust Boundary     |
| ARCH-NET-006 | Corporate-to-Backup Trust Boundary |
| ARCH-NET-007 | VirtualBox Network Isolation       |
| ARCH-NET-008 | Host-Based Windows Firewall        |
| ARCH-NET-009 | Active Directory DNS Traffic       |
| ARCH-NET-010 | Administrative Network Access      |

---

# 50. Network Architecture Traceability Example

```text
THR-OT-001
Corporate-to-OT Lateral Movement
        ↓
SEC-OBJ-15
Protect Manufacturing Systems
        ↓
ARCH-NET-003
Dedicated OT Zone
        ↓
ARCH-NET-005
Corporate-to-OT Trust Boundary
        ↓
PFSENSE01 Firewall Rules
        ↓
TEST-NET-OT-001
Unauthorised Access Test
        ↓
EVID-NET-OT-001
Firewall Rule + Block Log + Test
```

---

# 51. Current Lab vs Production Architecture

The current lab architecture is:

```text
              PFSENSE01
                  |
       +----------+----------+
       |          |          |
 Corporate/Mgmt   OT       Backup
       |
 DC / Servers /
 Users / Admin
```

This provides meaningful security boundaries for OT and backup systems.

However, it does not provide strong internal separation within the Corporate environment.

---

# 52. Recommended Production Architecture

A mature production environment could use:

```text
                         FIREWALL
                            |
    +----------+------------+-------------+----------+
    |          |            |             |          |
    v          v            v             v          v

Management    DC          Server        Corporate    Guest
  Zone        Zone         Zone          User Zone    Zone
    |
    +------------------------------------------+
                                               |
                                   Additional Security
                                        Boundaries
                                               |
                                  +------------+----------+
                                  |                       |
                                  v                       v
                              OT Zone                Backup Zone
```

Potential dedicated segments include:

* management VLAN;
* Domain Controller VLAN;
* server VLAN;
* corporate-user VLAN;
* guest VLAN;
* OT VLAN;
* backup VLAN.

---

# 53. Production Network Security Enhancements

A production implementation should consider:

* enterprise-managed switches;
* VLAN segmentation;
* dedicated management networks;
* dedicated firewall interfaces or trunked interfaces;
* redundant firewalls where required;
* network access control;
* intrusion detection/prevention;
* centralised network logging;
* secure remote access;
* VPN with strong authentication;
* network monitoring;
* dedicated privileged administration paths; and
* stronger separation of high-value identity infrastructure.

---

# 54. Known Residual Risk

Even with pfSense segmentation, the lab retains risk because:

* user endpoints and DC01 share a network;
* servers and user endpoints share a network;
* ADMIN01 shares the Corporate network;
* internal traffic may bypass pfSense;
* the environment has limited monitoring capabilities;
* the firewall is a single enforcement device; and
* high availability is not implemented.

These risks will be considered during the formal risk-assessment phase.

---

# 55. Security Architecture Decision

The three-zone architecture is therefore an intentional lab design decision.

```text
VirtualBox / Resource Constraint
          ↓
Three-Zone Design
          ↓
Reduced Internal Segmentation
          ↓
Compensating Controls
          |
          +---- Windows Firewall
          +---- GPO Hardening
          +---- Least Privilege
          +---- Privileged Account Separation
          +---- pfSense OT Isolation
          +---- pfSense Backup Isolation
          +---- Logging
          +---- Security Testing
```

The architecture does not claim that compensating controls make the combined Corporate zone equivalent to proper production segmentation.

---

# 56. Network Architecture Success Criteria

The network architecture will be considered successfully implemented when:

* all three security zones are operational;
* each zone uses its intended subnet;
* PFSENSE01 acts as the gateway for each zone;
* systems are connected to the correct VirtualBox network;
* critical systems use predictable addresses;
* Corporate clients receive correct DHCP configuration;
* domain clients use appropriate AD DNS;
* routing between zones passes through PFSENSE01;
* unrestricted Corporate-to-OT communication is prevented;
* unrestricted Corporate-to-Backup communication is prevented;
* unnecessary OT-to-Corporate traffic is prevented;
* unnecessary Backup-to-Corporate traffic is prevented;
* firewall rules are documented;
* firewall rules are tested;
* blocked traffic can be observed in pfSense logs;
* required communication is validated;
* residual risks are documented; and
* network evidence is retained.

---

# 57. Implementation Verification Checklist

The following will be verified during later implementation/testing phases:

* [ ] PFSENSE01 WAN operational
* [ ] Corporate interface `192.168.10.1/24`
* [ ] OT interface `192.168.20.1/24`
* [ ] Backup interface `192.168.30.1/24`
* [ ] Corporate network operational
* [ ] OT network operational
* [ ] Backup network operational
* [ ] Corporate DHCP operational
* [ ] Corporate DHCP range validated
* [ ] DC01 static addressing confirmed
* [ ] FILESERVER01 addressing confirmed
* [ ] ADMIN01 addressing confirmed
* [ ] CLIENT01 addressing confirmed
* [ ] OT01 addressing confirmed
* [ ] BACKUP01 addressing confirmed
* [ ] AD DNS settings validated
* [ ] Default gateways validated
* [ ] Corporate → OT restriction validated
* [ ] Corporate → Backup restriction validated
* [ ] OT → Backup restriction validated
* [ ] Firewall logging validated
* [ ] Required internet connectivity validated
* [ ] Security-zone diagram captured

---

# 58. Key Network Security Principle

The architecture is based on the principle:

> **Network connectivity should be treated as a privilege, not an assumption.**

A system should not be able to communicate with another security zone simply because a technical route exists.

Each significant cross-zone path should have:

1. a requirement;
2. an explicit security decision;
3. a firewall rule;
4. a validation test; and
5. evidence.

---

# 59. Document Status

| Field                 | Value                                 |
| --------------------- | ------------------------------------- |
| Project               | Active-Directory-Cybersecurity-Lab    |
| Document              | Network Security Architecture         |
| Organisation          | BlueWave Manufacturing Pty Ltd        |
| Virtualisation        | Oracle VirtualBox                     |
| Security Gateway      | PFSENSE01                             |
| Corporate Network     | `192.168.10.0/24`                     |
| Corporate Gateway     | `192.168.10.1`                        |
| Corporate DHCP        | `192.168.10.100-192.168.10.200`       |
| OT Network            | `192.168.20.0/24`                     |
| OT Gateway            | `192.168.20.1`                        |
| Backup Network        | `192.168.30.0/24`                     |
| Backup Gateway        | `192.168.30.1`                        |
| Security Zones        | 3                                     |
| Primary Network Model | Default Deny / Explicit Allow         |
| Status                | Network Security Architecture Defined |

