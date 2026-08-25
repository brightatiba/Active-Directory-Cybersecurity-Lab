# Windows Server Environment

## Objective

Deploy the Windows Server infrastructure required to support Active Directory, DNS and secured file services within the BlueWave cybersecurity lab.

Two Windows Server 2025 systems are used:

| Server       | IP Address      | Role                                   |
| ------------ | --------------- | -------------------------------------- |
| DC01         | `192.168.10.10` | Active Directory Domain Services / DNS |
| FILESERVER01 | `192.168.10.20` | Domain Member / File Server            |

Both servers reside within the Corporate / Management network (`192.168.10.0/24`).

---

## DC01

DC01 provides the core identity infrastructure for the `bluewave.local` domain.

**Configuration**

* Windows Server 2025 Standard Evaluation
* Static IP: `192.168.10.10`
* Gateway: `192.168.10.1`
* DNS: `192.168.10.10`
* Active Directory Domain Services
* DNS Server

Active Directory DNS functionality was validated using an LDAP SRV lookup:

```powershell
nslookup -type=SRV _ldap._tcp.dc._msdcs.bluewave.local
```

The query successfully identified:

```text
dc01.bluewave.local
192.168.10.10
TCP/389
```

---

## FILESERVER01

A dedicated member server was deployed rather than hosting business file services on the Domain Controller.

**Configuration**

* Windows Server 2025 Standard Evaluation
* Static IP: `192.168.10.20`
* Gateway: `192.168.10.1`
* DNS: `192.168.10.10`
* Domain: `bluewave.local`
* File Server role installed

Separating file services from DC01 reduces the role and service footprint of critical identity infrastructure and allows the file server to receive its own security baseline.

---

## Domain Integration

Before joining the domain, FILESERVER01 was validated for:

* pfSense gateway connectivity;
* DC01 connectivity;
* internal DNS resolution; and
* Active Directory SRV record discovery.

FILESERVER01 was then joined to:

`bluewave.local`

The domain relationship was verified using:

```powershell
Test-ComputerSecureChannel -Verbose
```

The secure-channel test returned successfully.

The server computer object was also moved from the default `Computers` container into the appropriate **Servers OU** to support server-specific Group Policy targeting.

---
<!--
## Validation

* [x] DC01 operating system and roles verified
* [x] DC01 static network configuration verified
* [x] Active Directory DNS validated
* [x] FILESERVER01 deployed
* [x] FILESERVER01 static network configuration validated
* [x] FILESERVER01 joined to `bluewave.local`
* [x] Domain secure channel validated
* [x] File Server role installed
* [x] FILESERVER01 moved to Servers OU
-->
---

## Security Outcome

The lab now separates critical Active Directory infrastructure from business file services.

DC01 is dedicated to identity and DNS services, while FILESERVER01 provides a separate platform for implementing and testing file permissions, SMB security, auditing, Group Policy and least-privilege access controls later in the project.

