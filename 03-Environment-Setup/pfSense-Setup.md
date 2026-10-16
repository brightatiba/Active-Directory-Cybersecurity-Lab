# pfSense Network Gateway Setup

## Objective

Configure pfSense as the central firewall and routing gateway for the Active Directory cybersecurity lab.

The firewall separates the environment into three security zones:

* Corporate / Management
* Manufacturing / OT
* Backup

---

## Network Configuration

| Interface       | Security Zone          | Address           |
| --------------- | ---------------------- | ----------------- |
| BWCORP (LAN)    | Corporate / Management | `192.168.10.1/24` |
| BWOT (OPT1)     | Manufacturing / OT     | `192.168.20.1/24` |
| BWBACKUP (OPT2) | Backup                 | `192.168.30.1/24` |

pfSense provides Layer 3 routing between the networks and will later enforce least-privilege inter-zone firewall rules.

---

## Corporate DHCP

DHCP is enabled on the BWCORP network.

| Setting         | Value                             |
| --------------- | --------------------------------- |
| Network         | `192.168.10.0/24`                 |
| DHCP Range      | `192.168.10.100 – 192.168.10.200` |
| Default Gateway | `192.168.10.1`                    |
| DNS Server      | `192.168.10.10`                   |

The DNS server distributed to Corporate clients was changed from the pfSense interface (`192.168.10.1`) to **DC01 (`192.168.10.10`)**.

This ensures future domain-joined clients use Active Directory DNS for domain and service discovery.

---

## Active Directory DNS Validation

DC01 uses a static network configuration:

| Setting | Value           |
| ------- | --------------- |
| IPv4    | `192.168.10.10` |
| Gateway | `192.168.10.1`  |
| DNS     | `192.168.10.10` |
| DHCP    | Disabled        |

The Windows DNS service was confirmed as running.

External DNS resolution was successfully tested, and Active Directory service discovery was validated using:

```powershell
nslookup -type=SRV _ldap._tcp.dc._msdcs.bluewave.local
```

The query successfully returned:

```text
dc01.bluewave.local
192.168.10.10
LDAP TCP/389
```

This confirmed that the required Active Directory DNS SRV records were available.

---

## Firewall Management Hardening

During the baseline review, the pfSense WebGUI was found to be using unencrypted HTTP management.

The management protocol was changed from:

```text
HTTP
```

to:

```text
HTTPS
```

Administrative access over HTTPS was successfully tested after the change.

SSH remains disabled because remote shell administration is not currently required.

This reduces unnecessary management-plane exposure.

---

## Firewall Baseline Finding

Initial firewall rules contain broad access rules including:

```text
BWCORP net   → any
BWOT net     → any
BWBACKUP net → any
```

These rules are useful during initial environment deployment but do not provide the intended least-privilege segmentation.

### Security Risk

A compromised system may be able to communicate with other security zones more broadly than required.

### Subsequent Remediation

The network-security assessment later restricted the tested IPv4 inter-zone paths, while maintaining explicit DNS/NTP exceptions for OT and Backup test systems. The controls were validated using:

* connectivity testing;
* port-specific testing;
* negative access tests; and
* pfSense firewall logs.

The results and residual gaps are recorded in [Network Segmentation](../05-Security-Controls/Network-Segmentation.md).

---

## Validation and Evidence

The baseline gateway, zone interfaces, DHCP, Active Directory DNS resolution, HTTPS WebGUI and disabled SSH management were validated during environment setup. Broad initial firewall permissions were subsequently remediated and tested in [Network Segmentation](../05-Security-Controls/Network-Segmentation.md).

Supporting baseline and post-treatment pfSense screenshots are available in [Evidence/Network](../Evidence/Network/).

---

## Security Outcome

This task established pfSense as the central network gateway for the lab, corrected the Active Directory DNS distribution path, secured firewall administration using HTTPS, and identified excessive baseline firewall access that was subsequently addressed for the tested IPv4 paths.

