
# Hybrid Identity Lab Series — Shanthiraj Theetla

Real-world hybrid identity setup connecting on-premises Active Directory to Azure/Entra ID.
Built on a Proxmox VM inside Hyper-V, connected to Azure over WireGuard.

---

## Environment

| Component | Details |
|-----------|---------|
| On-prem DC | DC-Prox-01 (192.168.31.10) — WS2022, Proxmox |
| Cloud DC | DC-AZU (10.0.0.4) — Azure VM, Central India |
| Tunnel | WireGuard — 10.10.10.1 (on-prem) ↔ 10.10.10.2 (Azure) |
| Domain | Learnig.co.in (intentional — baked into AD, cannot rename) |
| Entra Connect | v2.5.79.0 — Password Hash Sync |
| Entra ID tenant | Shanthislaboutlook.onmicrosoft.com, P2 trial active |

---

## H-01 — On-Premises Domain Setup

**Goal:** Build a functional on-premises AD domain on Proxmox.

**What was done:**
- Deployed Windows Server 2022 VM on Proxmox (inside Hyper-V)
- Promoted as Primary Domain Controller
- Domain: Learnig.co.in (typo intentional — baked into AD forest, irreversible without rebuild)
- Configured NTP sync to time.windows.com (resolved AADSTS700027 cert error later)
- Created test users including testuser@learnig.co.in

**Key learning:**
NTP misconfiguration causes silent Entra Connect auth failures. Always sync DC time
before configuring directory sync.

---

## H-02 — UPN Suffix Configuration

**Goal:** Add a routable UPN suffix so synced users get clean Entra ID UPNs.

**What was done:**
- Added custom UPN suffix in Active Directory Domains and Trusts
- Updated user UPNs from @learnig.co.in to routable suffix
- Verified suffix appears in Entra ID after sync

**Key learning:**
Default AD UPNs use the internal domain name which is non-routable in Entra ID.
UPN suffix must match a verified domain in Entra ID for clean sync.

---

## H-03 — Entra Connect + Password Hash Sync

**Goal:** Sync on-prem users to Entra ID using Entra Connect with PHS.

**What was done:**
- Installed Entra Connect v2.5.79.0 on DC-Prox-01
- Configured Password Hash Sync (PHS) — simplest sync method, no ADFS required
- Verified testuser@learnig.co.in appeared in Entra ID as synced user
- Fixed AADSTS700027 certificate error caused by NTP drift on DC

**Sync method chosen:** Password Hash Sync
- No additional infrastructure needed
- Supports leaked credential detection in Entra ID P2
- Suitable for lab and most production scenarios

**Key learning:**
AADSTS700027 is almost always a time sync issue, not a certificate issue.
Fix the clock before touching certificates.

---

## H-04 — Azure Additional Domain Controller over WireGuard

**Goal:** Extend Learnig.co.in to Azure by promoting an Azure VM as an additional DC.

### Phase 1 — Tunnel setup

- Installed WireGuard on both DC-Prox-01 and DC-AZU
- Configured site-to-site tunnel: 10.10.10.1 (DC-Prox-01) ↔ 10.10.10.2 (DC-AZU)
- Confirmed bidirectional ping over tunnel

### Phase 2 — DNS fix

**Problem:** DC-Prox-01 was registering three IPs in DNS:
- 192.168.31.10 (LAN — unreachable from Azure)
- 10.10.10.1 (WireGuard — correct)
- 169.254.83.107 (Tailscale leftover — junk)

**Fix:** Disabled DNS registration on Ethernet and Tailscale adapters on DC-Prox-01:
```powershell
Set-DnsClient -InterfaceAlias "Ethernet" -RegisterThisConnectionsAddress $false
Set-DnsClient -InterfaceAlias "Tailscale" -RegisterThisConnectionsAddress $false
ipconfig /registerdns
```

### Phase 3 — Domain join

- Set DNS on DC-AZU to 10.10.10.1 (WireGuard IP of DC-Prox-01)
- Set custom DNS at Azure VNET level to survive reboots
- Confirmed nltest /dsgetdc:learnig.co.in returned DC-Prox-01 over WireGuard
- Domain joined DC-AZU to Learnig.co.in

### Phase 4 — DC promotion

```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

Install-ADDSDomainController `
  -DomainName "Learnig.co.in" `
  -Credential (Get-Credential) `
  -InstallDns:$true `
  -NoGlobalCatalog:$false `
  -SiteName "Default-First-Site-Name" `
  -DatabasePath "C:\Windows\NTDS" `
  -LogPath "C:\Windows\NTDS" `
  -SysvolPath "C:\Windows\SYSVOL" `
  -Force:$true
```

**Result:**
