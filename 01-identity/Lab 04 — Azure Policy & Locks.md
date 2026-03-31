# Lab 04 — Azure Policy & Locks
**Domain:** Identity & Governance 

## Overview
Configured Azure Policy to enforce governance standards and applied Resource
Locks to protect resources from accidental deletion or modification. Covered
built-in policy assignments, audit policies, policy initiatives, compliance
monitoring, and both Delete and Read-only lock types.

## Objectives Completed
- [x] Assigned built-in "Allowed locations" policy (Deny effect) to rg-lab-identity
- [x] Assigned "Audit VMs that do not use managed disks" policy at Subscription scope
- [x] Checked Policy Compliance dashboard for evaluation results
- [x] Created a Policy Initiative (initiative-lab-governance) and assigned to rg-lab-networking
- [x] Applied a Delete lock (lock-rg-compute-delete) to rg-lab-compute and verified block
- [x] Applied a Read-only lock (lock-rg-networking-readonly) to rg-lab-networking and verified block

## Key Concepts

```
Policy Assignment = Policy Definition + Scope + Parameters

Policies assigned in this lab:
  "Allowed locations"                        → rg-lab-identity     (Effect: Deny)
  "Audit VMs that do not use managed disks"  → Subscription scope  (Effect: Audit)

Policy Initiative:
  initiative-lab-governance → rg-lab-networking
    ├── Allowed locations
    └── Audit VMs that do not use managed disks

Resource Locks applied:
  lock-rg-compute-delete     → rg-lab-compute     (Type: Delete)
  lock-rg-networking-readonly → rg-lab-networking  (Type: Read-only)
```

## What I Learned
- **Deny** effect blocks non-compliant resource creation immediately; **Audit** effect only flags resources in the compliance dashboard without blocking
- Policy compliance evaluation can take up to **30 minutes** to reflect after assignment
- A **Policy Initiative** (policy set) groups multiple policies together — easier to assign and manage governance at scale
- **Delete lock** prevents resource/resource group deletion but still allows modifications
- **Read-only lock** prevents both deletion and any modifications — most restrictive lock type
- Locks apply to **all users including Owners and admins** — the lock must be removed first before any blocked action can proceed
- Locks are inherited — a lock on a Resource Group applies to all resources within it

## Policy Effects Reference

| Effect | What it does |
|---|---|
| `Deny` | Blocks non-compliant resource creation or modification |
| `Audit` | Allows but flags non-compliant resources in compliance dashboard |
| `Append` | Adds additional settings to a resource on creation |
| `DeployIfNotExists` | Deploys a related resource if it doesn't already exist |

## Lock Types Reference

| Lock Type | Blocks Delete | Blocks Modify |
|---|---|---|
| Delete | ✅ Yes | ❌ No |
| Read-only | ✅ Yes | ✅ Yes |

## Screenshots
| Step | Screenshot |
|------|-----------|
| Allowed locations policy assigned to rg-lab-identity | <img width="740" height="329" alt="image" src="https://github.com/user-attachments/assets/b70f8efe-9887-4851-a6c7-0c3bfd96a358" />

| Audit VMs policy assigned at Subscription scope | <img width="952" height="307" alt="image" src="https://github.com/user-attachments/assets/7f6649aa-fb86-4c15-873c-dfa4d66cfd87" />

| Policy compliance dashboard |<img width="926" height="376" alt="image" src="https://github.com/user-attachments/assets/0d40927f-4979-4660-9a25-c31def525551" />

| initiative-lab-governance created and assigned |<img width="910" height="302" alt="image" src="https://github.com/user-attachments/assets/87b0b726-d237-478a-875d-f0cbccee719c" />

| Delete lock on rg-lab-compute — deletion blocked |<img width="276" height="132" alt="image" src="https://github.com/user-attachments/assets/63e31447-8ca9-49b9-a2d5-d4fe1da56766" />

| Read-only lock on rg-lab-networking — modification blocked | <img width="949" height="428" alt="image" src="https://github.com/user-attachments/assets/c410cd29-365f-414c-b396-b4180ac90e51" />


---
*Part of my AZ-104 Azure Administrator certification lab series.*  
***Subscription: Shanthi's_Lab | Tenant: shanthislab***
