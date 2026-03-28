# Lab 01 — Manage Microsoft Entra ID Users

**Domain:** Identity & Governance 

## Overview
Managed Microsoft Entra ID user accounts using Azure Portal and Azure CLI.
Covered user creation, property configuration, license assignment, and the
soft-delete and restore lifecycle.

## Objectives Completed
- [x] Created a user via Azure Portal with job title and department
- [x] Created a user via Azure CLI with forced password change
- [x] Set usage location for license eligibility
- [x] Deleted and restored a soft-deleted user (30-day window)
- [x] Invited a B2B guest user via portal invitation

## Key Commands Used
```bash
# Create user via CLI
az ad user create \
  --display-name "Lab01-CLIUser" \
  --user-principal-name "lab01cli@tenant.onmicrosoft.com" \
  --password "TempPass@2024!" \
  --force-change-password-next-sign-in true

# List users filtered by display name
az ad user list --filter "startswith(displayName,'Lab01')" --output table

# Delete a user
az ad user delete --id "lab01cli@tenant.onmicrosoft.com"
```

## What I Learned
- Usage location must be set before assigning any Microsoft 365 license
- Soft-deleted users are recoverable for 30 days via Deleted Users blade
- Guest (B2B) users have UserType = Guest vs Member for internal users
- CLI is faster for bulk operations; Portal is better for reviewing properties

## Screenshots
| Step | Screenshot |
|------|-----------|
| Portal user created | <img width="938" height="394" alt="image" src="https://github.com/user-attachments/assets/b36d4074-b2b6-4cb5-9748-322997feb2a8" />

| CLI user created | <img width="959" height="218" alt="image" src="https://github.com/user-attachments/assets/16acc551-8ea0-43a7-a4f4-783c0f0938a1" />
|
| Deleted users blade |<img width="604" height="250" alt="image" src="https://github.com/user-attachments/assets/f8b8d5a0-5d25-4ac7-8f66-344011b2c56e" />
) |

---
*Part of my AZ-104 Azure Administrator certification lab series.*
*Subscription: Shanthi's_Lab | Tenant: shanthislab*
