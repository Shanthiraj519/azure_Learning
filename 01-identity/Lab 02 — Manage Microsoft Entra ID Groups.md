# Lab 02 — Manage Microsoft Entra ID Groups
**Domain:** Identity & Governance

## Overview
Managed Microsoft Entra ID groups using the Azure Portal. Covered creation of
Assigned and Dynamic membership groups, Microsoft 365 groups, group ownership,
and group nesting rules and limitations.

## Objectives Completed
- [x] Created a Security Group with Assigned membership (grp-lab-developers)
- [x] Created a Microsoft 365 Group (grp-lab-m365)
- [x] Created a Dynamic Security Group with attribute-based rule (department = IT)
- [x] Assigned a Group Owner for delegated management
- [x] Nested a Security group inside another Security group
- [x] Observed Direct Members vs All Members (transitive membership)

## Key Concepts

```
Dynamic Membership Rule used:
  Property  : department
  Operator  : Equals
  Value     : IT

Result: Any user with department = "IT" is auto-added to grp-lab-dynamic
```

## What I Learned
- Dynamic groups require Entra ID P1 or P2 license to configure
- Membership updates in Dynamic groups can take a few minutes to reflect after attribute changes
- Microsoft 365 groups **cannot** be nested inside Security groups — portal shows: *"Microsoft 365 groups are not allowed"*
- Only Security → Security group nesting is supported
- **Direct members** shows explicitly added users/groups; **All members** includes transitive members from nested groups
- Group Owners can manage membership without needing admin privileges

## Screenshots
| Step | Screenshot |
|------|-----------|
| Security group created (grp-lab-developers) | <img width="1803" height="783" alt="image" src="https://github.com/user-attachments/assets/78f9692e-7574-422d-b654-1a12eac1051f" />

| Dynamic group rule configured | <img width="1874" height="796" alt="image" src="https://github.com/user-attachments/assets/3cf07dd4-2bd7-4e76-85de-e11086729760" />

| user's auto-added after department set to IT | <img width="1914" height="881" alt="image" src="https://github.com/user-attachments/assets/6704141c-e73f-4891-8af1-ac457b12dd95" />

| Group owner assigned | <img width="1656" height="601" alt="image" src="https://github.com/user-attachments/assets/f32754f0-bdd6-4480-be67-d333bf4fd543" />

| Nested group added — grp-lab-dynamic inside grp-lab-developers | <img width="884" height="391" alt="image" src="https://github.com/user-attachments/assets/faf84313-1963-4d03-a732-aed02bc5c890" />

| M365 nesting error — "Microsoft 365 groups are not allowed" | <img width="677" height="373" alt="image" src="https://github.com/user-attachments/assets/8fe44fcd-6906-4680-8d69-d1da1c0ae29a" />

---
*Part of my AZ-104 Azure Administrator certification lab series.*  
*Subscription: Shanthi's_Lab | Tenant: shanthislab*
