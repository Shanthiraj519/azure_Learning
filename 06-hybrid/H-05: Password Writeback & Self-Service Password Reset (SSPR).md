# H-06: Password Writeback & Self-Service Password Reset (SSPR)

## Overview
Enabled Password Writeback in Microsoft Entra Connect and configured 
Self-Service Password Reset (SSPR) in Entra ID, allowing synced on-premises 
users to reset their passwords from the cloud and have those changes written 
back to on-premises Active Directory automatically.

## Environment
| Component | Details |
|-----------|---------|
| Entra ID Tenant | Shanthislaboutlook.onmicrosoft.com |
| On-Prem DC | dc-prx-01 (192.168.31.10) |
| AD Domain | learnig.co.in |
| Entra Connect Version | 2.5.79.0 |
| License Required | Entra ID P2 |
| SSPR Group | grp-lab-developers |

## Objectives
- Enable Password Writeback feature in Entra Connect
- Configure SSPR in Entra ID for a selected group
- Verify on-premises writeback client is active
- Confirm end-to-end password writeback is operational

## Key Concepts
- **Password Writeback:** Feature in Entra Connect that pushes cloud password 
  changes back to on-premises AD in real time — without it, cloud resets 
  create a password mismatch between cloud and on-prem
- **SSPR (Self-Service Password Reset):** Allows users to reset their own 
  passwords from the cloud without calling the helpdesk — reduces IT support 
  load significantly
- **On-premises Integration:** The Entra Connect writeback agent acts as the 
  bridge between cloud password changes and on-prem AD
- **Writeback Client:** The agent running on dc-prx-01 that receives password 
  change instructions from Entra ID and applies them to AD
- **SSPR Scope:** SSPR can be enabled for None, Selected (group-based), or 
  All users — group-based rollout is best practice in production

## Steps Performed

### Part 1 — Enable Password Writeback in Entra Connect
1. Opened **Microsoft Entra Connect Sync** on `dc-prx-01`
2. Clicked **Configure** → selected **Customize synchronization options**
3. Authenticated with Entra ID Global Admin credentials
4. Clicked through Connect Directories and Domain/OU Filtering (no changes)
5. On **Optional Features** page — checked **Password writeback**
6. **Ready to configure** page confirmed:
   - Enable Password writeback
   - Configure synchronization services on this computer
7. Clicked **Configure** — received **Configuration complete** confirmation
8. Synchronization process initiated automatically

### Part 2 — Configure SSPR in Entra ID
1. Navigated to **Entra ID Portal → Protection → Password reset → Properties**
2. Set **Self service password reset enabled** to **Selected**
3. Selected **grp-lab-developers** as the SSPR-enabled group
4. Clicked **Save** — received **"Password reset policy saved successfully"** confirmation

### Part 3 — Verify On-premises Integration
1. Navigated to **Password reset → On-premises integration**
2. Confirmed:
   - **"Your on-premises writeback client is up and running"** ✅
   - **Microsoft Entra Connect Sync agent** — Status: Set up complete ✅
   - **Enable password write back for synced users** — checked ✅

## Learnings
- Password Writeback must be enabled in **both** places — in Entra Connect 
  (on-prem side) AND verified in Entra ID portal (cloud side) — missing 
  either step will break the flow
- SSPR scope is group-based only for non-admin users — individual user 
  assignment is not supported
- The writeback client status in On-premises integration is the quickest 
  way to verify the entire writeback pipeline is healthy
- Entra Connect cloud sync agent (Not detected) is separate from the 
  classic Entra Connect Sync agent — both can coexist but serve different 
  deployment models
- In production, always test SSPR with a pilot group before enabling for 
  all users to avoid support issues

## Screenshots
| Step | Description |
|------|-------------|
| ![Connect Welcome]<img width="1303" height="924" alt="image" src="https://github.com/user-attachments/assets/b6f6f1c6-d7e7-4e7a-bd6a-ff5d035d8650" />
 | Entra Connect Sync welcome screen on dc-prx-01 |
| ![Ready to Configure]<img width="1316" height="905" alt="image" src="https://github.com/user-attachments/assets/09de5c05-1033-4f11-988a-26f2e125919e" />
 | Password Writeback listed in Ready to Configure summary |
| ![Config Complete]<img width="1400" height="842" alt="image" src="https://github.com/user-attachments/assets/96cf3860-2caf-4d8f-994a-49fb42227317" />
<img width="1309" height="924" alt="image" src="https://github.com/user-attachments/assets/229f010e-3bc7-4e53-9037-e08ec4379985" />
 <img width="1316" height="919" alt="image" src="https://github.com/user-attachments/assets/77de8cb0-a665-4207-b2ca-34f25b8a29c8" />
| Configuration complete — sync process initiated |
| ![SSPR Properties]<img width="1288" height="926" alt="image" src="https://github.com/user-attachments/assets/f85a81e2-9de0-49c2-91c2-0f75d412a892" />
<img width="1288" height="926" alt="image" src="https://github.com/user-attachments/assets/8c1d4feb-8c82-4419-9e2f-75527a95def8" />
 | SSPR enabled for grp-lab-developers group |
| ![On-premises Integration]<img width="1288" height="952" alt="image" src="https://github.com/user-attachments/assets/22ddab6f-49ba-4905-8092-2ccf2404d091" />
 <img width="1568" height="756" alt="image" src="https://github.com/user-attachments/assets/d11c45c1-38dc-4b31-8365-f5878b39bf1b" />
<img width="1483" height="706" alt="image" src="https://github.com/user-attachments/assets/9d2640a1-5af2-4fe3-8082-9a101d2c3c88" />
<img width="1568" height="628" alt="image" src="https://github.com/user-attachments/assets/ad721e74-c8eb-4151-9dc4-89ca76560675" />
| Writeback client running — Set up complete |
