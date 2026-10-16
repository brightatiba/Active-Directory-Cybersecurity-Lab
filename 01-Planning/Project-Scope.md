# Project Scope — BlueWave Active Directory Security & GRC Lab

## Business Context
BlueWave Manufacturing Pty Ltd is a fictional Australian industrial manufacturer. The lab represents a small enterprise with Windows identity infrastructure, a corporate network, a simulated manufacturing/OT zone and a backup zone. The project is a **GRC-focused security case study**, with hands-on Active Directory administration as supporting evidence.

## Objective
Assess selected identity, authentication and network-access risks; implement proportionate controls; verify results; and report risk, remediation, residual exposure and relevant framework alignment.

## In Scope
- Oracle VirtualBox lab with pfSense and three IPv4 networks: BWCORP `192.168.10.0/24`, BWOT `192.168.20.0/24` and BWBACKUP `192.168.30.0/24`.
- Windows Server 2025 AD DS/DNS (DC01), domain-joined file server (FILESERVER01), standard client (CLIENT01) and admin workstation (ADMIN01).
- OU design, departmental users/groups, separated administrative identity and review of privileged memberships.
- Selected domain password/lockout policies, workstation Defender, Windows Firewall and audit policy.
- pfSense least-privilege IPv4 segmentation with DNS/NTP exceptions for OT01 and BACKUP01.
- Before/after technical tests, a four-scenario risk assessment, risk register and control mapping.

## Delivered Artifacts
- [Architecture overview](../02-Architecture/Architecture-Overview.md)
- [AD implementation](../04-Active-Directory/AD-Implementation.md) and [security review](../04-Active-Directory/AD-Security-Review.md)
- [GPO controls](../05-Security-Controls/GPO-Security-Hardening.md) and [network controls](../05-Security-Controls/Network-Segmentation.md)
- [Risk assessment](../06-Security-Assessment/Security-Risk-Assessments.md), [risk register](../07-GRC/Risk-Register.md) and [control mapping](../07-GRC/Control-Mapping.md)

## Out of Scope / Not Claimed
Full production-grade AD hardening; SIEM deployment; application allowlisting; MFA rollout; backup creation/restoration testing; a functional OT control system; fully approved OT/backup traffic flows; complete IPv6 segmentation assessment; ISO/IEC 27001 certification; or an ASD Essential Eight maturity rating.

Evidence images will be uploaded separately to [Evidence](../Evidence/) when available. No unuploaded screenshot is represented as publicly verifiable.

## Completion Criteria
A control counts as *configured* when its effective settings are observed, and as *tested* only for the connection paths or events actually validated. Findings distinguish technical remediation from provisional business residual risk and formal risk-owner acceptance.

**Scope status:** Completed for the selected case-study objectives, subject to deferred screenshot uploads and any remaining repository polish.
