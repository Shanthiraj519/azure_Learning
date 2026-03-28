# Conditional Access — Require MFA for all users

**Date:** March 2026  
**Service:** Microsoft Entra ID — Conditional Access (P2)  
****Cert topic**: Manage identity and access

---

## Objective

Enforce Multi-Factor Authentication (MFA) for all users in the tenant using
a Conditional Access policy. This is a zero-trust baseline control — no user
can access any cloud app without completing MFA.

---

## Policy configuration

| Setting | Value |
|--------|-------|
| Policy name | Require multifactor authentication for all users |
| Target users | All users |
| Exclude | Break-glass admin account (shanthislab@outlook.com) |
| Target apps | All cloud apps |
| Grant control | Require multifactor authentication |
| Policy state | On |

---

## Steps taken

1. Navigate to **Entra ID → Protection → Conditional Access → Policies**
2. Click **+ New policy**
3. Name: `Require multifactor authentication for all users`
4. Under **Users** → Include: `All users`
5. Under **Users** → Exclude: add your admin/break-glass account
6. Under **Target resources** → Include: `All cloud apps`
7. Under **Grant** → select `Require multifactor authentication`
8. Set **Enable policy** to `On`
9. Click **Save**

---

## Why exclude the admin account?

Always exclude at least one break-glass admin account from CA policies.
If MFA is misconfigured or an auth method fails, you need a way back in.
This is a production best practice, not optional.

---

## What I learned

- Conditional Access policies are evaluated at sign-in time, not at account creation
- Report-only mode lets you test policy impact before enforcing — useful in production before enabling
- Admin exclusion from CA policies must be paired with strong password + monitoring to avoid becoming a security gap
---

## Screenshots

<img width="939" height="431" alt="image" src="https://github.com/user-attachments/assets/ba2cd60e-d83a-419a-bcf8-8821d2f29084" />

