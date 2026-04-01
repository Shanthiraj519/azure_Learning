# Lab 06 – Network Security Groups (NSGs)

**Domain:** 2 – Networking  
 
**Environment:** Shanthi's_Lab | East US | shanthislab@outlook.com

---

## Objective

Create and configure Network Security Groups (NSGs) to control inbound and outbound traffic to Azure subnets and VMs using priority-based security rules.

---

## Resources Created

| Resource | Type | Details |
|----------|------|---------|
| nsg-lab-web | Network Security Group | Associated with web-subnet (10.0.1.0/24) |
| nsg-lab-db | Network Security Group | Associated with db-subnet (10.0.2.0/24) |

---

## NSG Rules Configured

### nsg-lab-web

| Priority | Rule Name | Port | Protocol | Source | Action |
|----------|-----------|------|----------|--------|--------|
| 100 | Allow-SSH-Inbound | 22 | TCP | Any | Allow |
| 110 | Allow-HTTP-Inbound | 80 | TCP | Any | Allow |
| 4000 | Deny-All-Inbound-Custom | * | Any | Any | Deny |

### nsg-lab-db

| Priority | Rule Name | Port | Protocol | Source | Action |
|----------|-----------|------|----------|--------|--------|
| 100 | Allow-MySQL-From-WebSubnet | 3306 | TCP | 10.0.1.0/24 | Allow |
| 4000 | Deny-All-To-DB | * | Any | Any | Deny |

---

## Tasks Completed

- [x] Created nsg-lab-web and added inbound rules (SSH, HTTP, Deny All)
- [x] Associated nsg-lab-web with web-subnet
- [x] Created nsg-lab-db with restricted MySQL access from web-subnet only
- [x] Associated nsg-lab-db with db-subnet
- [x] Verified Effective Security Rules on vm-lab-linux-01
- [x] Tested rules using IP Flow Verify in Network Watcher

---

## Screenshots

> Take all screenshots in the Azure Portal. Upload them to the `screenshots/` folder inside `lab-06-nsg/`.

| # | File Name | What to Capture | Where in Portal |
|---|-----------|-----------------|-----------------|
| 1 | <img width="1331" height="598" alt="image" src="https://github.com/user-attachments/assets/ba4c806c-fd92-423b-98dd-ab29f31e4cbf" />
 | nsg-lab-web overview page showing Name, Resource Group (rg-lab-networking), Location (Central india), and Subscription | Network Security Groups > nsg-lab-web > Overview |
| 2 | `<img width="1303" height="475" alt="image" src="https://github.com/user-attachments/assets/00602b40-fe48-4956-81f9-d548d6c64341" />
 | Inbound security rules list showing all 3 rules — Allow-SSH-Inbound (1000), Allow-HTTP-Inbound (1010), Deny-All-Inbound-Custom (4000) with Priority, Port, and Action columns visible | nsg-lab-web > Inbound security rules |
| 3 | <img width="1354" height="303" alt="image" src="https://github.com/user-attachments/assets/6a09a2b4-2432-4311-b80f-a71ba5f6fca5" />
 | Subnets blade showing nsg-lab-web is associated with vnet-lab-centralindia / web-subnet | nsg-lab-web > Subnets |
| 4 | <img width="1333" height="322" alt="image" src="https://github.com/user-attachments/assets/dbcbe4b3-e2eb-4678-b574-0a3ce71d669a" />
 | nsg-lab-db overview page showing Name, Resource Group (rg-lab-networking), Location (centralindia), and Subscription | Network Security Groups > nsg-lab-db > Overview |
| 5 | <img width="1365" height="475" alt="image" src="https://github.com/user-attachments/assets/0b8e7659-47d2-45b4-86e6-031039e424da" />
| Inbound security rules list showing Allow-MySQL-From-WebSubnet (100, source 10.0.1.0/24, port 3306) and Deny-All-To-DB (4000) | nsg-lab-db > Inbound security rules |
| 6 | <img width="1360" height="332" alt="image" src="https://github.com/user-attachments/assets/4e08ecb5-9c21-450a-b939-9b114581d7e3" />
 | Subnets blade showing nsg-lab-db is associated with vnet-lab-eastus / db-subnet | nsg-lab-db > Subnets |
| 7 | `07-vnet-subnet-nsg-mapping.png` | VNet subnets list showing both web-subnet and db-subnet with their respective NSGs (nsg-lab-web and nsg-lab-db) visible in the NSG column | Virtual Networks > vnet-lab-eastus > Subnets |
| 8 | `08-effective-security-rules.png` | Effective security rules page showing combined inbound rules applied to vm-lab-linux-01 NIC — all 3 custom rules + default rules visible | VM > vm-lab-linux-01 > Networking > NIC > Effective security rules |
| 9 | `09-ip-flow-verify-allowed.png` | IP Flow Verify result showing Access allowed for port 22 (SSH) inbound — rule name Allow-SSH-Inbound visible in result | Network Watcher > IP flow verify > result screen |
| 10 | `10-ip-flow-verify-denied.png` | IP Flow Verify result showing Access denied for port 8080 inbound — rule name Deny-All-Inbound-Custom visible in result | Network Watcher > IP flow verify > result screen |
