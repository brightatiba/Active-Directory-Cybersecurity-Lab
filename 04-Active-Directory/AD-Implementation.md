# Active Directory Implementation

## 1. Overview

Active Directory Domain Services (AD DS) was deployed and configured in the BlueWave Manufacturing cybersecurity lab to provide centralised identity management, authentication and administrative control.

The implementation demonstrates practical Windows Server administration skills and establishes the identity infrastructure needed for security assessment, Group Policy implementation and access-control validation.

**Implementation status:** Completed  
**Domain:** `bluewave.local`  
**Platform:** Windows Server 2025 Standard Evaluation  
**Virtualisation:** Oracle VirtualBox

## 2. Infrastructure Configuration

The Active Directory environment consists of the following systems:

| System | Role | IP Address | Security Zone |
|---|---|---|---|
| DC01 | Domain Controller, AD DS, DNS | `192.168.10.10` | BWCORP |
| FILESERVER01 | Domain-joined File Server | `192.168.10.20` | BWCORP |
| CLIENT01 | Standard Windows 11 workstation | DHCP | BWCORP |
| ADMIN01 | Dedicated Windows 11 administrative workstation | DHCP | BWCORP |

The BWCORP network uses `192.168.10.0/24`, with pfSense at `192.168.10.1` providing the network gateway and DHCP services.

Domain-joined systems use DC01 (`192.168.10.10`) for Active Directory DNS resolution.

Manufacturing/OT and Backup systems operate in separate network zones and are not joined to the Active Directory domain.

## 3. Domain Controller Deployment

DC01 was configured with the following server roles:

- Active Directory Domain Services
- DNS Server

The Active Directory forest and domain were established using the internal DNS namespace `bluewave.local`.

### Configuration

| Setting | Value |
|---|---|
| Hostname | DC01 |
| Operating System | Windows Server 2025 Standard Evaluation |
| Domain | `bluewave.local` |
| IPv4 Address | `192.168.10.10/24` |
| Default Gateway | `192.168.10.1` |
| Preferred DNS | `192.168.10.10` |
| IP Assignment | Static |

### Validation

The following activities were completed successfully:

- Confirmed that the DNS Server service was running.
- Validated internal domain name resolution.
- Verified Active Directory LDAP service records.
- Verified Kerberos service discovery.
- Completed `dcdiag /test:dns` successfully.

Example validation commands:

```powershell
Get-Service DNS

nslookup dc01.bluewave.local

nslookup -type=SRV _ldap._tcp.dc._msdcs.bluewave.local

nslookup -type=SRV _kerberos._tcp.bluewave.local

dcdiag /test:dns
```

**Result:** Domain Controller DNS services and Active Directory service discovery were successfully validated.

## 4. Organisational Unit Design

A structured OU hierarchy was implemented to support identity administration, logical organisation and future Group Policy targeting.

The environment includes:

| Organisational Unit | Purpose |
|---|---|
| Admin Accounts | Dedicated privileged identities |
| Admin Workstations | Administrative endpoint policy targeting |
| Servers | Domain-joined member servers |
| Workstations | Standard corporate endpoints |
| Users | Standard employee identities |
| Service Accounts | Reserved for controlled service identities |
| Groups | Departmental and administrative security groups |
| Departmental OUs | Logical organisation of employee accounts |
| Domain Controllers | Domain Controller administration and GPO targeting |

Departmental user organisation covers Executive, Finance, Human Resources, Engineering, Information Technology, Operations, Sales and Warehouse.

The structure supports different security policies for standard endpoints, servers and administrative systems.

**Security consideration:** OUs provide organisational and Group Policy scope; they are not independent security boundaries.

## 5. User and Security Group Administration

User accounts were created to represent employees across BlueWave Manufacturing's business departments.

Departmental security groups were implemented using a consistent naming convention.

| Security Group | Intended Purpose |
|---|---|
| GG-Executive-Users | Executive department membership |
| GG-Finance-Users | Finance department membership |
| GG-HR-Users | Human Resources membership |
| GG-Engineering-Users | Engineering department membership |
| GG-IT-Users | Standard IT employee membership |
| GG-IT-Administrators | IT administrative identity grouping |
| GG-Operations-Users | Operations department membership |
| GG-Sales-Users | Sales department membership |
| GG-Warehouse-Users | Warehouse department membership |

These groups establish the foundation for role-based access administration.

Group membership and access permissions will be validated against business resource requirements when access-control testing is implemented.

## 6. Administrative Identity Separation

Separate accounts were created for standard and privileged activities.

| Account | Purpose | Group Membership |
|---|---|---|
| `lwalker` | Standard IT employee account | Domain Users, GG-IT-Users |
| `admin-lwalker` | Dedicated administrative identity | Domain Users, GG-IT-Administrators |

The administrative identity was initially a member of Domain Admins. A subsequent security review identified excessive standing privileges, resulting in removal of that membership.

The dedicated `ADMIN01` workstation was deployed and placed in the Admin Workstations OU to support administrative workstation security policies.

**Security outcome:** Standard and administrative identities are separated, and unnecessary standing Domain Admin membership has been removed.

The detailed risk assessment, remediation and before-and-after evidence are recorded in [AD Security Review](AD-Security-Review.md).

## 7. Member Server and Workstation Integration

FILESERVER01, CLIENT01 and ADMIN01 were successfully joined to the `bluewave.local` domain.

Computer objects were placed in the designated OUs.

| Computer | Organisational Unit |
|---|---|
| DC01 | Domain Controllers |
| FILESERVER01 | Servers |
| CLIENT01 | Workstations |
| ADMIN01 | Admin Workstations |

### Domain Membership Validation

```powershell
Get-CimInstance Win32_ComputerSystem |
Select-Object Name, Domain, PartOfDomain
```

### Secure Channel Validation

```powershell
Test-ComputerSecureChannel -Verbose
```

### Computer Inventory

```powershell
Get-ADComputer -Filter * |
Select-Object Name, Enabled, DistinguishedName
```

**Validation results:**

- All three member systems successfully joined the domain.
- Their computer objects were visible in Active Directory.
- Domain secure-channel tests returned `True`.
- Computer OU placement was verified.
- CLIENT01 successfully discovered the domain's Kerberos service through DNS.

FILESERVER01 was also configured with the File Server role, providing infrastructure for subsequent group-based resource access-control exercises.

## 8. Implementation Evidence

The Active Directory deployment was validated using PowerShell and Windows administrative tools.

Relevant evidence includes:

- Server role and DNS validation
- Domain membership and secure-channel results
- Organisational Unit and computer placement
- User and security group configuration
- Standard and privileged identity separation

Supporting screenshots are retained under:

`Evidence/Active-Directory/`

Security assessment and remediation evidence is referenced separately in `AD-Security-Review.md`.

## 9. Security and Business Outcomes

The implementation provides the foundation for:

- Centralised employee identity administration
- Domain authentication and DNS-based service discovery
- Departmental group-based access control
- Separate standard and administrative identities
- Role-specific workstation and server policies
- Privileged access assessment
- Security control validation and audit evidence collection

The environment also enables practical assessment of identity and access management controls against relevant requirements in ISO/IEC 27001, NIST Cybersecurity Framework and ASD Essential Eight.

## 10. Conclusion

Active Directory Domain Services was successfully deployed and validated within the BlueWave Manufacturing lab.

The implementation demonstrates practical experience in Windows Server administration, DNS configuration, domain integration, OU design, user and group management, administrative identity separation and PowerShell-based validation.

Subsequent project work focuses on applying, testing and assessing security controls rather than expanding the Active Directory infrastructure unnecessarily.

**Implementation status: Completed.**
