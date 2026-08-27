# Windows Client and Administrative Workstation Deployment

## Overview

Two Windows workstations were deployed and joined to the `bluewave.local` Active Directory domain to support security configuration and validation throughout the lab.

| Host | Purpose | Network | AD Placement |
|---|---|---|---|
| `CLIENT01` | Standard corporate workstation | BWCORP | Workstations OU |
| `ADMIN01` | Privileged administrative workstation | BWCORP | Administrative Workstations OU |

The separation between standard and administrative workstations provides a foundation for implementing privileged-access controls and different Group Policy security baselines.

---

## Network Configuration

Both workstations use DHCP from pfSense on the BWCORP network.

| Setting | Value |
|---|---|
| Network | `192.168.10.0/24` |
| DHCP Server | `192.168.10.1` |
| Default Gateway | `192.168.10.1` |
| DNS Server | `192.168.10.10` |
| DHCP Pool | `192.168.10.100–192.168.10.200` |

`CLIENT01` received `192.168.10.105` during deployment.
`ADMIN01` received `192.168.10.106` during deployment.

DC01 (`192.168.10.10`) is distributed as the DNS server so domain clients can locate Active Directory services.

---

## Pre-Domain Validation

Network and Active Directory DNS connectivity were verified before joining the workstations to the domain.

Tests included:

```powershell
ping 192.168.10.1
ping 192.168.10.10

nslookup dc01.bluewave.local
nslookup -type=SRV _ldap._tcp.dc._msdcs.bluewave.local
```

---

## Domain Integration

`CLIENT01` and `ADMIN01` were joined to:

`bluewave.local`

Domain membership was verified on `DC01` using:

```powershell
Get-CimInstance Win32_ComputerSystem | 
Select-Object Name, Domain, PartofDomain
```
The wworkstation trust relationship with Active Directorywas validated using:

```poweshell
Test-ComputerSecureChannel -Verbose
```

Both workstations returned a valid secure channel.

---

## Security Outcome

Standard user activity and privileged administrative activity now have separate workstation environments.

This provides the technical foundation for implementing and testing least privilege, privileged account separation, workstation-specific Group Policy, administrative logon restrictions, and endpoint security controls later in the project.























