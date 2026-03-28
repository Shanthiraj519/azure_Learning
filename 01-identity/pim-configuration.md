# Privileged Identity Management (PIM) — Just-in-time role assignment

**Date:** March 2026  
**Service:** Microsoft Entra ID — Privileged Identity Management (P2)  
**Cert topic:** : Manage identity and access / Identity Governance

---

## Objective

Remove permanent privileged role assignments and replace with just-in-time
(JIT) eligible assignments using PIM. Users must explicitly activate the role
when needed, with a time-limited window — reducing the attack surface of
standing admin access.

---

## Configuration

| Setting | Value |
|--------|-------|
| Role configured | User Administrator |
| Assignment type | Eligible (not Active) |
| Members assigned | Shanthiraj Theetla, Chintu |
| Scope | Directory |
| Duration | Permanently eligible |
| Activation required | Yes — user must request activation |

---

## Steps taken

1. Navigate to **Identity Governance → Privileged Identity Management →
   Microsoft Entra roles**
2. Click **Assign Eligibility**
3. Search for and select **User Administrator** role
4. On Membership tab → click **+ Add members** → select test users
5. On Setting tab → Assignment type: **Eligible**, Permanently eligible: checked
6. Click **Assign**
7. Verified in **User Administrator → Assignments → Eligible assignments**
   — both users appear with Permanent end time

---

## What I learned

- **Eligible vs Active**: Eligible means the user *can* request the role.
  Active means they have it right now. PIM enforces the difference.
- **Permanently eligible ≠ permanently active**: The user still has to
  activate the role each time — it just never expires as an option.
- **Standing access is a security risk**: A permanently Active Global Admin
  account is a target. Eligible assignments reduce that window to near zero.
- In production, you would also configure: maximum activation duration,
  require MFA on activation, require justification, and require approval.

---


---

## Screenshots
<img width="950" height="275" alt="image" src="https://github.com/user-attachments/assets/19cca19e-65b8-4891-933d-df498ca61402" />

