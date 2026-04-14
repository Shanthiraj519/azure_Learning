# H-03: Conditional Access — Require MFA for All Users

## Overview
Configured a Conditional Access policy in Microsoft Entra ID to enforce 
Multi-Factor Authentication (MFA) for all users, excluding a break-glass 
admin account. This is a foundational Zero Trust security control ensuring 
no user can access cloud resources without completing MFA.

## Environment
| Component | Details |
|-----------|---------|
| Entra ID Tenant | Shanthislaboutlook.onmicrosoft.com |
| Policy Name | CA-01 — Require MFA for All Users |
| License Required | Entra ID P2 |
| Break-Glass Account | gbadmin@Shanthislaboutlook.onmicrosoft.com |

## Objectives
- Create a break-glass Global Administrator account excluded from all CA policies
- Configure a Conditional Access policy enforcing MFA for all users
- Verify the policy applies correctly to synced on-premises users
- Ensure break-glass account is excluded to prevent tenant lockout

## Key Concepts
- **Conditional Access (CA):** Policy engine in Entra ID that enforces access 
  controls based on signals like user, location, device, and app
- **Multi-Factor Authentication (MFA):** Requires users to verify identity 
  using a second factor (authenticator app, SMS, etc.) beyond just a password
- **Break-Glass Account:** A cloud-only Global Administrator account excluded 
  from all CA policies — used for emergency access if normal admin accounts 
  are locked out
- **Zero Trust:** Security model where no user or device is trusted by default — 
  always verify, always enforce controls
- **Grant Controls:** The action CA takes when conditions are met — in this case, 
  granting access only after MFA is completed

## Steps Performed
1. Created break-glass account `gbadmin@Shanthislaboutlook.onmicrosoft.com` 
   as a cloud-only user in Entra ID
2. Assigned **Global Administrator** role to gbadmin
3. Navigated to **Entra ID Portal → Protection → Conditional Access → New Policy**
4. Named the policy `CA-01 — Require MFA for All Users`
5. Configured **Users:**
   - Include: All users
   - Exclude: gbadmin (break-glass account)
6. Configured **Target Resources:**
   - Include: All cloud apps
7. Configured **Grant:**
   - Grant access
   - Require multifactor authentication
8. Set policy state to **On** and clicked **Create**
9. Verified policy appeared in CA dashboard with status **On**
10. Tested by signing in as a synced user — MFA prompt appeared as expected

## Why Break-Glass Account Matters
If a CA policy is misconfigured and locks out all users including admins, 
the break-glass account (excluded from all policies) allows recovery access 
to the tenant. Without it, a misconfigured policy could result in a complete 
tenant lockout with no recovery path.

## Learnings
- Always create and exclude a break-glass account **before** enabling any 
  CA policy — never after
- Cloud-only accounts are preferred for break-glass as they are not dependent 
  on on-prem AD or Entra Connect sync
- CA policies with **All users** and **All cloud apps** are broad — in production, 
  start with a subset of users in report-only mode before enforcing
- **Report-only mode** is available in CA to test policy impact without 
  enforcing it — useful in production environments
- Synced on-premises users are also subject to CA policies once they sign 
  into cloud apps

## Screenshots
| Step | Description |
|------|-------------|
| ![Break-Glass]<img width="953" height="387" alt="image" src="https://github.com/user-attachments/assets/3f59a815-7633-494d-8ba3-180f61829c33" />
 | gbadmin created with Global Administrator role |
| ![CA Policy]<img width="957" height="422" alt="image" src="https://github.com/user-attachments/assets/a9a23607-c7f5-43df-ac62-6e9a4b13f2bd" />
 | CA-01 policy listed in Conditional Access dashboard |
| ![Policy Details]<img width="905" height="403" alt="image" src="https://github.com/user-attachments/assets/92253ae8-3ea7-46b0-903c-2805ebf4f8c2" />
 | Policy configuration — users, apps, and grant controls |
| ![MFA Prompt]<img width="904" height="464" alt="image" src="https://github.com/user-attachments/assets/8036aff1-9802-4d01-beb2-4cc5b2df28a4" />
<img width="1116" height="634" alt="image" src="https://github.com/user-attachments/assets/f3cdd781-3cfa-42c9-93b4-807abf3e2792" />
 | MFA prompt triggered on user sign-in |
| ![Sign-in Logs]<img width="950" height="398" alt="image" src="https://github.com/user-attachments/assets/96bba281-d867-4999-ad5f-d28a5f5d0dd3" />
 | Sign-in logs confirming CA policy applied |
