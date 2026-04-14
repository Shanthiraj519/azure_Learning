# H-04: Privileged Identity Management (PIM) — JIT Role Activation

## Overview
Configured Privileged Identity Management (PIM) in Microsoft Entra ID to enable 
Just-In-Time (JIT) privileged access for a synced on-premises user (testuser). 
Assigned the User Administrator role as an eligible assignment and validated 
end-to-end JIT activation by activating the role and performing an administrative 
task to prove access was granted.

## Environment
| Component | Details |
|-----------|---------|
| Entra ID Tenant | Shanthislaboutlook.onmicrosoft.com |
| License Required | Entra ID P2 |
| Test User | testuser@learnig.co.in (synced from on-prem AD) |
| Role Assigned | User Administrator |
| Assignment Type | Eligible (JIT) |

## Objectives
- Assign Entra ID P2 license to a synced on-premises user
- Configure PIM eligible role assignment for testuser
- Activate the role JIT as testuser and validate access
- Prove role activation by performing an administrative action

## Key Concepts
- **Privileged Identity Management (PIM):** Entra ID service that provides 
  Just-In-Time privileged access to Azure and Entra ID roles, reducing the 
  risk of excessive standing permissions
- **Eligible Assignment:** User does not have the role permanently — they must 
  explicitly activate it when needed, with justification
- **Active Assignment:** Role is currently active and in use — time-bound 
  by default (1 hour in this lab)
- **Just-In-Time (JIT) Access:** Access is granted only when needed and 
  automatically expires — core Zero Trust principle
- **Standing Access Risk:** Permanently assigned privileged roles are a security 
  risk — if the account is compromised, the attacker has persistent elevated access. 
  PIM eliminates this by making access temporary and auditable

## Steps Performed

### Part 1 — Assign P2 License to testuser
1. Navigated to **Microsoft 365 Admin Center** → Users → Active Users
2. Opened **Test User** → Licenses and apps tab
3. Set location to **India**
4. Checked **Microsoft Entra ID P2** (99 of 100 licenses available)
5. Clicked **Save changes**

### Part 2 — Configure PIM Eligible Assignment
1. Navigated to **Entra ID Portal** → ID Governance → 
   Privileged Identity Management
2. Clicked **Microsoft Entra roles** → Roles
3. Searched for and selected **User Administrator**
4. Clicked **Add assignments** → selected **testuser@learnig.co.in**
5. Assignment type set to **Eligible**
6. Clicked **Assign** — testuser appeared in eligible assignments list

### Part 3 — JIT Activation as testuser
1. Opened incognito browser → signed in as `testuser@learnig.co.in`
2. Navigated to **PIM → My roles → Microsoft Entra roles**
3. Found **User Administrator** in eligible assignments
4. Clicked **Activate** → entered justification for activation
5. Role activated successfully — visible in **Active assignments** tab
6. State showed **Activated** with end time **4/14/2026, 12:29:56 PM**

### Part 4 — Validate Access
1. As testuser, navigated to **Entra ID → Users**
2. Created a new user to prove User Administrator role was active
3. **"Successfully created user"** confirmation appeared
4. Confirmed new user visible in users list (11 users total)

## Learnings
- P2 license must be assigned to a user before they can use PIM features — 
  assigning via M365 Admin Center is one valid method
- Synced on-premises users can be assigned PIM eligible roles just like 
  cloud-only users
- JIT activation requires a justification by default — this creates an 
  audit trail of why access was requested
- Role activation is time-bound — after expiry the user loses the elevated 
  access automatically without any manual intervention
- PIM audit logs capture every activation, making privileged access fully 
  traceable — critical for compliance and security investigations
- Always deactivate roles manually after use in production — don't rely 
  solely on automatic expiry

## Screenshots
| Step | Description |
|------|-------------|
| ![PIM Overview]<img width="959" height="426" alt="image" src="https://github.com/user-attachments/assets/1dd3597a-2674-4778-a1b4-c15634383918" />
 | PIM Quick Start page in Entra ID |
| ![P2 License]<img width="1512" height="797" alt="image" src="https://github.com/user-attachments/assets/eece2395-7051-44f7-a6ea-0defdb6a5426" />
| Entra ID P2 license assigned to testuser in M365 Admin Center |
| ![Eligible Assignment]<img width="953" height="400" alt="image" src="https://github.com/user-attachments/assets/ef90337f-851d-432c-ac05-0198438ad739" />
 | testuser assigned as eligible for User Administrator role |
| ![Activation Request]<img width="1568" height="510" alt="image" src="https://github.com/user-attachments/assets/8cde89f9-2f60-44b3-a6aa-62b908d4907f" />
| testuser activating User Administrator role with justification |
| ![Active Assignment]<img width="959" height="357" alt="image" src="https://github.com/user-attachments/assets/3e01513a-2bcb-4646-a1f9-24f94bade432" />
 | Role in Active assignments — State: Activated with end time |
| ![User Created]<img width="1568" height="595" alt="image" src="https://github.com/user-attachments/assets/d9f0779a-187f-4d10-833a-e351ece8d7fb" />
 | testuser successfully created a new user proving role access |
