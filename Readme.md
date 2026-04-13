# Azure Administration Lab — Shanthiraj Theetla
Hands-on Azure lab environment documenting real configurations across identity,
networking, compute, security, and automation.

**Certifications:** AZ-900 · AZ-104  
**In progress:** AZ-500 (Azure Security Engineer)  
**Lab subscription:** Shanthi's_Lab (Entra ID P2 enabled)  
**LinkedIn:** (https://www.linkedin.com/in/shanthiraj-theetla-076a58194/)

---

## Lab areas

| Area | Topics covered | Status |
|------|---------------|--------|
| [01 — Identity](./01-identity/) | Entra ID, RBAC, Conditional Access, PIM, Identity Protection | Active |
| [02 — Networking](./02-networking/) | VNet, NSG, VNet Peering | Active |
| [03 — Compute](./03-compute/) | Linux VM, Managed Identity, Key Vault access | Active |
| [04 — Security](./04-security/) | Defender for Cloud, Key Vault, Secure Score | Active |
| [05 — Automation](./05-automation/) | PowerShell, Azure Policy, bulk provisioning | Active |
| [06 — Hybrid Identity](./06-hybrid/) | On-prem AD, Entra Connect, Azure DC, WireGuard tunnel | Active |

---

## Hybrid lab environment

A separate hands-on series extending on-premises Active Directory into Azure.

| Component | Details |
|-----------|---------|
| On-prem DC | DC-Prox-01 — Windows Server 2022, Proxmox, domain Learnig.co.in |
| Cloud DC | DC-AZU — Azure VM, promoted as additional DC |
| Connectivity | WireGuard site-to-site tunnel (10.10.10.0/24) |
| Directory sync | Entra Connect 2.5.79.0 — Password Hash Sync |
| Entra ID | Hybrid users synced, P2 features enabled |

**Hybrid lab series:** H-01 through H-10 covering domain setup, Entra Connect,
Conditional Access, PIM, Password Writeback, and Identity Protection on synced users.

---

## Lab environment

- **Subscription:** Shanthi's_Lab (Pay-as-you-go / Free tier)
- **Entra ID tenant:** Custom domain, P2 trial active
- **Deployed resources:** VNet + subnets, NSGs, Linux VM, Entra ID users/groups
- **On-prem:** Proxmox hypervisor running inside Hyper-V, AD domain Learnig.co.in

---

## Lab journal

See [lab-journal.md](./lab-journal.md) for a running log of tasks completed,
issues encountered, and key learnings.
