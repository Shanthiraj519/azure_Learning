# Lab 06 – Network Security Groups (NSGs) — 

Hands-on lab creating and configuring Network Security Groups to control
inbound and outbound traffic to Azure subnets using priority-based rules.

---

## Environment

| Component | Details |
|-----------|---------|
| Subscription | Shanthi's_Lab |
| Resource Group | rg-lab-networking |
| Region | Central India |
| VNet | vnet-lab-centralindia — 10.0.0.0/16 |
| Web Subnet | web-subnet — 10.0.1.0/24 |
| DB Subnet | db-subnet — 10.0.2.0/24 |

---

## Objective

Create and configure Network Security Groups (NSGs) to control inbound and
outbound traffic to Azure subnets and VMs using priority-based security rules.

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

## Steps

1. Navigated to **Network Security Groups** in the Azure portal and clicked **+ Create**.
2. Created `nsg-lab-web` in resource group `rg-lab-networking`, region Central India.
3. Added inbound rules: Allow-SSH-Inbound (port 22), Allow-HTTP-Inbound (port 80), Deny-All-Inbound-Custom (port *, priority 4000).
4. Associated `nsg-lab-web` with `web-subnet` under `vnet-lab-centralindia`.
5. Created `nsg-lab-db` in the same resource group and region.
6. Added inbound rules: Allow-MySQL-From-WebSubnet (port 3306, source 10.0.1.0/24), Deny-All-To-DB (port *, priority 4000).
7. Associated `nsg-lab-db` with `db-subnet` under `vnet-lab-centralindia`.
8. Verified **Effective Security Rules** on `vm-lab-linux-01` NIC — confirmed all custom rules appear alongside Azure default rules.
9. Used **IP Flow Verify** in Network Watcher to confirm port 22 is allowed and port 8080 is denied.

---

## Key Concepts

**Network Security Group (NSG)** is a firewall layer in Azure that contains
inbound and outbound security rules. Rules are evaluated by priority —
lowest number wins. Azure also injects default rules (65000, 65001, 65500)
that cannot be deleted but can be overridden with lower priority rules.

**NSGs are stateful** — if inbound traffic is allowed, the return outbound
traffic is automatically permitted without needing a separate outbound rule.

**Subnet vs NIC association** — an NSG can be associated at the subnet level
(applies to all VMs in the subnet) or at the NIC level (applies to a single VM).
Both can be applied simultaneously; traffic must pass both.

**Priority-based evaluation** — rules are processed from lowest to highest
number. Once a rule matches, processing stops. The explicit Deny-All at
priority 4000 ensures any traffic not matched by earlier Allow rules is blocked.

**IP Flow Verify** — a Network Watcher tool that tests whether a specific
traffic flow (source IP, destination IP, port, protocol, direction) would be
allowed or denied by the NSG, and identifies which rule made the decision.

**Effective Security Rules** — shows the combined set of rules applied to a
NIC, merging subnet-level and NIC-level NSG rules, including Azure defaults.

---

## Learnings

- NSG rules are stateful — allowing inbound SSH on port 22 automatically
  permits the return traffic without a separate outbound rule.
- Priority 4000 for the Deny-All rule leaves room to insert future Allow rules
  at priorities 200–3999 without renumbering existing rules.
- Restricting the DB subnet to only accept MySQL traffic from the web subnet
  CIDR (10.0.1.0/24) demonstrates micro-segmentation — a core security pattern.
- Effective Security Rules is the fastest way to confirm which rules are
  actually applied to a VM, especially when both subnet and NIC NSGs are in use.
- IP Flow Verify saves time during troubleshooting by pinpointing exactly
  which rule is blocking or allowing a specific flow.
- Azure default rules (AllowVNetInBound, AllowAzureLoadBalancerInBound,
  DenyAllInBound) are always present and cannot be deleted — custom rules
  override them by using lower priority numbers.

---

## Screenshots

| # | File Name | Description |
|---|-----------|-------------|
| 01 | 01-nsg-web-overview.png | nsg-lab-web overview — Name, Resource Group, Location, Subscription |
| 02 | 02-nsg-web-inbound-rules.png | Inbound rules showing Allow-SSH (100), Allow-HTTP (110), Deny-All (4000) |
| 03 | 03-nsg-web-subnet-association.png | Subnets blade showing nsg-lab-web associated with web-subnet |
| 04 | 04-nsg-db-overview.png | nsg-lab-db overview — Name, Resource Group, Location, Subscription |
| 05 | 05-nsg-db-inbound-rules.png | Inbound rules showing Allow-MySQL-From-WebSubnet (100) and Deny-All-To-DB (4000) |
| 06 | 06-nsg-db-subnet-association.png | Subnets blade showing nsg-lab-db associated with db-subnet |
| 07 | 07-vnet-subnet-nsg-mapping.png | VNet subnets list showing both subnets with their NSGs in the NSG column |
| 08 | 08-effective-security-rules.png | Effective security rules on vm-lab-linux-01 NIC — custom + default rules visible |
| 09 | 09-ip-flow-verify-allowed.png | IP Flow Verify — port 22 allowed, rule Allow-SSH-Inbound shown in result |
| 10 | 10-ip-flow-verify-denied.png | IP Flow Verify — port 8080 denied, rule Deny-All-Inbound-Custom shown in result |
