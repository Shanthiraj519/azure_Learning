<img width="1919" height="758" alt="image" src="https://github.com/user-attachments/assets/d78d93f6-1076-45ec-80dc-1d990afdf25e" /># Lab 05 — Virtual Networks & Subnets

**Domain:** 2 — Networking
**Lab Number:** 05 of 20
**Region:** Central India (`centralindia`)
**Resource Group:** `rg-lab-networking`

---

## Objective

Create a Virtual Network (VNet) in Central India with three subnets representing a three-tier architecture — web, database, and management. This forms the networking foundation for all Domain 2 labs.

---

## Environment Details

| Field | Value |
|---|---|
| Subscription | Shanthi's Lab |
| Tenant | Shanthi's_Lab |
| Resource Group | `rg-lab-networking` |
| Region | Central India |
| VNet Name | `vnet-lab-centralindia` |
| VNet Address Space | `10.0.0.0/16` |

---

## Subnet Configuration

| Subnet Name | Address Prefix | Purpose |
|---|---|---|
| `snet-web` | `10.0.1.0/24` | Web / Frontend tier |
| `snet-db` | `10.0.2.0/24` | Database / Backend tier |
| `snet-mgmt` | `10.0.3.0/24` | Management / Bastion |

> **Note:** Azure reserves 5 IP addresses per subnet. A `/24` provides 251 usable IPs.

---

## Steps Performed

### 1. Verified Resource Group in Central India

```bash
az group list --output table
```

Created `rg-lab-networking` in Central India (previously East US — switched due to resource availability).

```bash
az group create \
  --name rg-lab-networking \
  --location centralindia
```

---

### 2. Created the Virtual Network

```bash
az network vnet create \
  --name vnet-lab-centralindia \
  --resource-group rg-lab-networking \
  --location centralindia \
  --address-prefix 10.0.0.0/16
```

---

### 3. Created Subnets

```bash
# Web subnet
az network vnet subnet create \
  --name snet-web \
  --vnet-name vnet-lab-centralindia \
  --resource-group rg-lab-networking \
  --address-prefix 10.0.1.0/24

# Database subnet
az network vnet subnet create \
  --name snet-db \
  --vnet-name vnet-lab-centralindia \
  --resource-group rg-lab-networking \
  --address-prefix 10.0.2.0/24

# Management subnet
az network vnet subnet create \
  --name snet-mgmt \
  --vnet-name vnet-lab-centralindia \
  --resource-group rg-lab-networking \
  --address-prefix 10.0.3.0/24
```

---

### 4. Verified Configuration

```bash
az network vnet subnet list \
  --vnet-name vnet-lab-centralindia \
  --resource-group rg-lab-networking \
  --output table
```

**Output:**

```
Name       AddressPrefix    ProvisioningState
---------  ---------------  -----------------
snet-web   10.0.1.0/24      Succeeded
snet-db    10.0.2.0/24      Succeeded
snet-mgmt  10.0.3.0/24      Succeeded
```

---

## Key Concepts Learned

- **VNet** — Isolated network in Azure, scoped to a single region
- **Subnets** — Logical segments within a VNet for workload separation
- **CIDR** — `/16` gives 65,536 IPs across the VNet; `/24` gives 251 usable IPs per subnet
- **Reserved IPs** — Azure reserves 5 IPs per subnet (network, gateway, 2× DNS, broadcast)
- **Region note** — VNets are regional; cross-region communication requires VNet Peering

---

## Screenshots

> - <img width="1919" height="758" alt="image" src="https://github.com/user-attachments/assets/34556bc4-2e9e-4625-89d9-5f56fc7b64cf" />
 — VNet overview page in Azure Portal
> - `<img width="959" height="337" alt="image" src="https://github.com/user-attachments/assets/163529ed-451c-46ff-bca0-501026387a15" />

 — Subnets blade showing all three subnets
> - <img width="1368" height="217" alt="image" src="https://github.com/user-attachments/assets/2f40590d-e963-493b-a423-84e0dbc1c0cd" />
 — Terminal output of subnet list command

---


---

*Part of the [AZ-104 Lab Series](../../README.md) — shanthirajtheetla/azure_Learning*
