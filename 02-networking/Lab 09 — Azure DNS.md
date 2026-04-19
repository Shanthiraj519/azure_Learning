# Lab 09 — Azure DNS

## Overview
This lab covers Azure DNS configuration including Public DNS Zones with multiple record types and Private DNS Zones with VNet integration and auto-registration. It demonstrates how Azure handles both internet-facing and internal name resolution for cloud resources.

## Environment

| Setting | Value |
|---------|-------|
| Subscription | Shanthi's Lab |
| Resource Group | rg-lab-networking |
| Region | Central India (Global for DNS) |
| Public DNS Zone | learnig.co.in |
| Private DNS Zone | shanthi.internal |
| VNet | vnet-lab-centralindia |

## Objectives
- Create a Public DNS Zone and add A, CNAME, and TXT record sets
- Create a Private DNS Zone
- Link Private DNS Zone to a VNet with auto-registration enabled
- Add a manual A record in the Private DNS Zone
- Understand the difference between public and private DNS resolution in Azure

## Key Concepts

| Concept | Description |
|---------|-------------|
| Public DNS Zone | Internet-facing zone; requires NS delegation at domain registrar |
| Private DNS Zone | Internal VNet resolution only; not accessible from internet |
| SOA Record | Start of Authority — auto-created, defines zone properties |
| NS Record | Name Server — auto-created for public zones, not needed for private |
| A Record | Maps hostname to IPv4 address |
| CNAME Record | Alias record pointing to another hostname; cannot be used at zone apex (@) |
| TXT Record | Stores text data; commonly used for SPF, domain verification |
| Auto-registration | VMs in linked VNet automatically register their hostname and private IP |
| TTL | Time-to-live — how long resolvers cache the DNS record |

## Steps Performed

### Part 1 — Public DNS Zone

**1. Create Public DNS Zone**

```powershell
# Via Portal: DNS Zones → Create
# Resource Group : rg-lab-networking
# Name           : learnig.co.in
# Location       : Global (auto-assigned)
```

**2. Add A Record**

```powershell
New-AzDnsRecordSet -ResourceGroupName "rg-lab-networking" `
  -ZoneName "learnig.co.in" `
  -Name "www" `
  -RecordType A `
  -Ttl 300 `
  -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.0.0.10")
```

**3. Add CNAME Record**

```powershell
New-AzDnsRecordSet -ResourceGroupName "rg-lab-networking" `
  -ZoneName "learnig.co.in" `
  -Name "blog" `
  -RecordType CNAME `
  -Ttl 300 `
  -DnsRecords (New-AzDnsRecordConfig -Cname "www.learnig.co.in")
```

**4. Add TXT Record**

```powershell
New-AzDnsRecordSet -ResourceGroupName "rg-lab-networking" `
  -ZoneName "learnig.co.in" `
  -Name "@" `
  -RecordType TXT `
  -Ttl 300 `
  -DnsRecords (New-AzDnsRecordConfig -Value "v=spf1 include:outlook.com ~all")
```

### Part 2 — Private DNS Zone

**5. Create Private DNS Zone**

```powershell
New-AzPrivateDnsZone -ResourceGroupName "rg-lab-networking" `
  -Name "shanthi.internal"
```

**6. Link Private DNS Zone to VNet with Auto-registration**

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName "rg-lab-networking" `
  -Name "vnet-lab-centralindia"

New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "rg-lab-networking" `
  -ZoneName "shanthi.internal" `
  -Name "link-vnet-lab" `
  -VirtualNetworkId $vnet.Id `
  -EnableRegistration
```

**7. Add Manual A Record in Private Zone**

```powershell
New-AzPrivateDnsRecordSet -ResourceGroupName "rg-lab-networking" `
  -ZoneName "shanthi.internal" `
  -Name "appserver" `
  -RecordType A `
  -Ttl 300 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.0.0.20")
```

## DNS Record Summary

### Public Zone — learnig.co.in

| Name | Type | TTL | Value |
|------|------|-----|-------|
| @ | NS | 172800 | ns1–ns4.azure-dns.* |
| @ | SOA | 3600 | azuredns-hostmaster.microsoft.com |
| @ | TXT | 300 | v=spf1 include:outlook.com ~all |
| blog | CNAME | 300 | www.learnig.co.in |
| www | A | 300 | 10.0.0.10 |

### Private Zone — shanthi.internal

| Name | Type | TTL | Value | Auto Registered |
|------|------|-----|-------|----------------|
| @ | SOA | 3600 | azureprivatedns-host.microsoft.com | False |
| appserver | A | 300 | 10.0.0.20 | False |

## VNet Link Configuration

| Setting | Value |
|---------|-------|
| Link Name | link-vnet-lab |
| Virtual Network | vnet-lab-centralindia |
| Link Status | Completed |
| Auto-Registration | Enabled |
| Fallback to Internet | Disabled |

## Learnings
- Public DNS Zones are **Global** resources in Azure — not tied to any region
- Azure auto-creates **SOA** and **NS** records for public zones; private zones only get SOA
- **CNAME cannot be used at the zone apex (@)** — use an A record or Alias record instead
- Private DNS Zones require a **Virtual Network Link** before any VM can resolve records
- **Auto-registration** means VMs in the linked VNet register their hostname automatically — the `Auto registered` column in recordsets will show `True` for these entries
- Private zones are only resolvable from within the linked VNet — not from the internet

## Known Limitations / Future Extensions
- Public DNS zone `learnig.co.in` is not delegated to Azure NS at GoDaddy — internet resolution will not work until NS records are updated at the registrar
- No live VM was available to test auto-registration during this lab — a future extension would spin up a VM in `vnet-lab-centralindia` and verify its hostname appears automatically in `shanthi.internal`
- Private DNS resolution verification via `nslookup appserver.shanthi.internal` from a VM is a recommended follow-up step

## Screenshots

| Screenshot | Description |
|------------|-------------|
| /<img width="1919" height="766" alt="image" src="https://github.com/user-attachments/assets/6685c90f-fc05-46bf-b565-371a01cde869" />
 | Public DNS Zone learnig.co.in overview with 4 name servers |
|<img width="1479" height="797" alt="image" src="https://github.com/user-attachments/assets/592b9012-a18f-4f22-8457-15faa2c77a34" />
 | All 5 record sets in public zone (NS, SOA, TXT, CNAME, A) |
| <img width="729" height="397" alt="image" src="https://github.com/user-attachments/assets/3178b539-cca9-4da3-990c-8a4b52a5faa5" />
| Private DNS Zone shanthi.internal overview |
| <img width="1568" height="667" alt="image" src="https://github.com/user-attachments/assets/fbe83018-c8d1-448c-860b-2a967c7fa807" />
 | VNet link status Completed with auto-registration enabled |
| <img width="1568" height="675" alt="image" src="https://github.com/user-attachments/assets/e6b3a51d-b398-42cc-8dba-cfc2bb0f527e" />
 | Private zone recordsets showing appserver A record |
