# Group Policy Security Hardening

## 1. Overview

A Windows security baseline was implemented in the BlueWave Manufacturing cybersecurity lab using Active Directory Group Policy.

The objective was to strengthen authentication controls, endpoint protection, host firewall configuration and security auditing while collecting evidence of control implementation.

**Domain:** `bluewave.local`  
**Domain Controller:** DC01  
**Test Workstation:** CLIENT01  
**Workstation GPO:** `GPO-BW-Workstation-Security-Baseline`  
**Implementation Status:** Completed

## 2. Scope

The implementation included:

- Domain password and account lockout controls
- Microsoft Defender Antivirus
- Windows Defender Firewall
- Advanced Windows audit policy

Domain account policies were configured at the domain level. Endpoint security policies were configured through the workstation security baseline GPO linked to the Workstations OU.

CLIENT01 was used to validate workstation policy application. Administrative workstations and servers were not included in the initial workstation baseline scope.

## 3. Domain Account Policy Hardening

The initial assessment identified a disabled account lockout threshold and a seven-character minimum password requirement.

### Before-and-After Configuration

| Security Setting | Before | After |
|---|---|---|
| Account lockout threshold | 0 (Disabled) | 10 attempts |
| Account lockout duration | 10 minutes | 15 minutes |
| Reset account lockout counter | 10 minutes | 15 minutes |
| Minimum password length | 7 characters | 14 characters |
| Password complexity | Enabled | Enabled |

### Validation

The effective default domain account policy was verified using:

```powershell
Get-ADDefaultDomainPasswordPolicy |
Select-Object LockoutThreshold,
              LockoutDuration,
              LockoutObservationWindow,
              MinPasswordLength,
              ComplexityEnabled
```

**Result:** The effective default domain policy matched the target configuration.

## 4. Microsoft Defender Antivirus

Microsoft Defender Antivirus settings were configured using the workstation security baseline GPO.

### Validation

On CLIENT01:

```powershell
Get-MpComputerStatus |
Select-Object AMServiceEnabled,
              AntivirusEnabled,
              RealTimeProtectionEnabled
```

| Setting | Verified Result |
|---|---|
| AMServiceEnabled | True |
| AntivirusEnabled | True |
| RealTimeProtectionEnabled | True |

**Result:** Microsoft Defender Antivirus and real-time protection were operational.

The workstation baseline GPO was also confirmed as applied.

## 5. Windows Defender Firewall

Firewall baseline settings were configured for all three Windows network profiles.

| Profile | Enabled | Default Inbound | Default Outbound |
|---|---|---|---|
| Domain | True | Block | Allow |
| Private | True | Block | Allow |
| Public | True | Block | Allow |

### Validation

```powershell
Get-NetFirewallProfile -PolicyStore ActiveStore |
Select-Object Name, Enabled,
              DefaultInboundAction,
              DefaultOutboundAction
```

**Result:** All three profiles matched the configured security baseline.

An initial validation issue was encountered when CLIENT01 reported a broken domain secure channel. The secure channel was subsequently repaired and validated successfully.

The root cause of the initial trust failure was not established, so it was not attributed to the firewall configuration.

Post-recovery validation confirmed:

- Workstation security GPO applied successfully.
- Firewall settings matched the target baseline.
- Domain secure channel returned `True`.

## 6. Advanced Audit Policy

Windows auditing was configured to support authentication monitoring and security investigation.

| Audit Subcategory | Verified Configuration |
|---|---|
| Logon | Success and Failure |
| Credential Validation | Success and Failure |
| Process Creation | Success and Failure |

### Validation

```powershell
auditpol /get /subcategory:"Logon","Credential Validation","Process Creation"
```

The Windows Security event log was examined to confirm the presence of relevant events.

| Event ID | Description | Result |
|---|---|---|
| 4624 | Successful logon | Observed |
| 4688 | Process created | Observed |
| 4625 | Failed logon | Not tested |

**Result:** The selected auditing settings were active, and successful logon and process creation events were recorded.

## 7. Group Policy Validation

The workstation security baseline was verified using:

```powershell
gpresult /r /scope computer
```

**Result:** `GPO-BW-Workstation-Security-Baseline` appeared in CLIENT01's applied Group Policy objects.

Individual security settings were additionally checked using PowerShell and `auditpol`.

## 8. Security Findings and Remediation

| Finding | Risk | Corrective Action | Status |
|---|---|---|---|
| GPO-01: Account lockout disabled | Increased exposure to password guessing | Configured account lockout policy | Remediated |
| GPO-02: Weak minimum password length | Increased exposure to password compromise | Increased minimum length to 14 characters | Remediated |
| GPO-03: Domain secure-channel interruption | Workstation domain authentication disruption | Repaired and revalidated secure channel | Resolved; root cause undetermined |

The findings will be considered in the consolidated GRC risk assessment and control-evidence mapping.

## 9. Evidence

Supporting screenshots (relative to the repository root) are linked directly below:

| Control evidence | Screenshot |
|---|---|
| Domain account policy before hardening | [Before policy](../Evidence/GPO/domain-password-policy-before.png) |
| Domain account policy after hardening | [After policy](../Evidence/GPO/domain-password-policy-after.png) |
| CLIENT01 Defender operational status | [Defender validation](../Evidence/GPO/CLIENT01-defender-status.png) |
| CLIENT01 Windows Firewall configuration | [Firewall validation](../Evidence/GPO/CLIENT01-firewall-validation.png) |
| Advanced auditing settings | [Audit policy evidence](../Evidence/GPO/client01-advanced-auditing.png) |

The applied workstation GPO was checked using `gpresult`; no separate GPResult screenshot is currently committed. The supporting command/result is documented in Section 7.

## 10. GRC Outcome

This exercise demonstrates:

- Identification of security configuration weaknesses
- Implementation of preventive and detective security controls
- Centralised security-policy management
- Verification of effective endpoint configurations
- Evidence collection for security assessments
- Remediation and post-change validation

The implemented controls provide supporting technical evidence for subsequent assessment against relevant ISO/IEC 27001, NIST Cybersecurity Framework and ASD Essential Eight requirements, where applicable.

## 11. Conclusion

The selected Windows security baseline was successfully implemented and validated on CLIENT01.

The exercise demonstrates practical Group Policy administration and evidence-based security control assessment without attempting to represent a full enterprise security baseline.

**Status: Completed.**
