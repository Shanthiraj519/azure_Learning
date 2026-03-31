# Lab 03 — Role-Based Access Control (RBAC)
**Domain:** Identity & Governance 

## Overview
Configured Azure Role-Based Access Control (RBAC) using the Azure Portal.
Covered role assignments at different scopes (Subscription and Resource Group),
group-based access, effective permission verification, custom role creation,
and role removal.

## Objectives Completed
- [x] Assigned built-in Reader role to testuser1 at Resource Group scope (rg-lab-identity)
- [x] Assigned built-in Contributor role to grp-lab-developers at Resource Group scope (rg-lab-compute)
- [x] Assigned built-in Reader role to testuser2 at Subscription scope
- [x] Verified role assignments and checked effective permissions using "Check access"
- [x] Created a Custom Role (VM_Manager) with VM start, stop, and restart permissions
- [x] Removed Reader role assignment for testuser1 from rg-lab-identity

## Key Concepts

```
RBAC Role Assignment = Security Principal + Role + Scope

Scopes (top to bottom — permissions inherit downward):
  Management Group
      └── Subscription
              └── Resource Group
                      └── Resource

Roles assigned in this lab:
  testuser1          → Reader      → rg-lab-identity (Resource Group scope) [later removed]
  testuser2          → Reader      → Subscription scope (inherited by all RGs)
  grp-lab-developers → Contributor → rg-lab-compute (Resource Group scope)

Custom Role — VM_Manager:
  Microsoft.Compute/virtualMachines/read
  Microsoft.Compute/virtualMachines/start/action
  Microsoft.Compute/virtualMachines/powerOff/action
  Microsoft.Compute/virtualMachines/restart/action
```

## What I Learned
- RBAC role assignment = Security Principal + Role Definition + Scope — all three are required
- Permissions are **additive** — a user with Reader at subscription and Contributor at RG level gets the higher permission at that RG
- Roles assigned at a higher scope **inherit downward** — testuser2's Subscription-level Reader role applies to all resource groups automatically
- Group-based assignments are preferred over individual assignments — easier to manage at scale
- **Check access** blade is useful to verify a user's effective permissions at any scope
- Custom roles allow fine-grained control — VM_Manager can start/stop/restart VMs without having full Contributor access
- Removing a role assignment immediately revokes that access — no delay

## Screenshots
| Step | Screenshot |
|------|-----------|
| Reader role assigned to testuser1 on rg-lab-identity | <img width="959" height="326" alt="image" src="https://github.com/user-attachments/assets/e5423dd2-29cb-48d4-a29d-3f27d6c5f487" />

| Contributor role assigned to grp-lab-developers on rg-lab-compute | <img width="953" height="295" alt="image" src="https://github.com/user-attachments/assets/e4b5be79-89aa-4cb5-85fd-143c0bbfb5a1" />

| Reader role assigned to testuser2 at Subscription scope |<img width="952" height="361" alt="image" src="https://github.com/user-attachments/assets/f09a870f-b637-4108-8654-3b5a31abe7a1" />

| Role assignments tab showing all assignments | <img width="947" height="395" alt="image" src="https://github.com/user-attachments/assets/733426e4-8cb3-4754-8d14-c883f451ea88" />

| Check access — testuser1 effective permissions | <img width="959" height="425" alt="image" src="https://github.com/user-attachments/assets/0eb0733b-8877-45e2-ad81-67a39c00f0a8" />

| VM_Manager custom role definition | <img width="817" height="395" alt="image" src="https://github.com/user-attachments/assets/f6743090-9bdf-45f5-bbb6-2a9616c66ee3" />

| Reader role removed from testuser1 on rg-lab-identity |<img width="953" height="314" alt="image" src="https://github.com/user-attachments/assets/bb0194d4-f5b5-49ef-a83c-35c94b73cb90" />


---
*Part of my AZ-104 Azure Administrator certification lab series.*  
*Subscription: Shanthi's_Lab | Tenant: shanthislab*
