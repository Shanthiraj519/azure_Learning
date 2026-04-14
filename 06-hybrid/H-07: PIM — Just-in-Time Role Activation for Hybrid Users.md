# H-07: PIM — Just-in-Time Role Activation for Hybrid Users

## Overview
Configured Privileged Identity Management (PIM) in Microsoft Entra ID to enforce 
just-in-time (JIT) privileged access for synced hybrid users. PIM eliminates 
standing administrative privileges by requiring users to explicitly activate 
eligible roles for a limited time window, with optional justification and 
approval workflows.

## Environment
| Component | Details |
|-----------|---------|
| Entra ID Tenant | Shanthislaboutlook.onmicrosoft.com |
| License Required | Entra ID P2 |
| Role Configured | User Administrator |
| Test User | testuser@learnig.co.in (synced hybrid user) |
| Break-Glass Account | BGAdmin@Shanthislaboutlook.onmicrosoft.com |

## Objectives
- Understand the difference between permanent and eligible role assignments
- Configure a PIM eligible role assignment for a synced hybrid user
- Activate a role using the PIM activation workflow
- Review activation history and audit logs in PIM

## Key Concepts
- **PIM (Privileged Identity Management):** Entra ID P2 service that provides 
  JIT privileged access, enforcing time-bound role activations instead of 
  permanent standing assignments
- **Eligible Assignment:** The user is granted the *right* to activate a role 
  but does not hold it permanently — they must activate it when needed
- **Active Assignment:** The user holds the role permanently with no activation 
  step required — considered a higher-risk configuration
- **JIT Activation:** The process of a user requesting and receiving a role for 
  a defined time window (e.g. 1–8 hours), after which it expires automatically
- **Activation Controls:** PIM can require justification, MFA, or manager 
  approval before a role is granted — configurable per role
- **Hybrid User in PIM:** Synced users from on-premises AD (via Entra Connect) 
  are fully supported in PIM — their cloud identity is what PIM manages

## Steps Performed

### Part 1 — Navigate to PIM and Select Role
1. Navigated to **Entra ID Portal → Identity Governance → Privileged Identity Management**
2. Selected **Entra roles** under Manage
3. Clicked **Roles → User Administrator**
4. Clicked **Assignments → Add assignments**

### Part 2 — Configure Eligible Assignment
1. Set **Assignment type:** Eligible
2. Selected **testuser@learnig.co.in** as the member
3. Set assignment duration: **Permanently eligible** (for lab purposes)
4. Clicked **Assign**
5. Verified the assignment appeared under the **Eligible** tab

### Part 3 — Configure Role Settings (Activation Controls)
1. From the **User Administrator** role, clicked **Settings**
2. Reviewed and configured:
   - **Activation maximum duration:** 8 hours
   - **Require justification on activation:** ✅ Enabled
   - **Require MFA on activation:** ✅ Enabled
   - **Require approval:** ❌ Not required (lab simplification)
3. Saved settings

### Part 4 — Activate the Role as the Test User
1. Signed in as **testuser@learnig.co.in**
2. Navigated to **PIM → My roles → Eligible assignments**
3. Located **User Administrator** → clicked **Activate**
4. Entered justification: "Testing PIM JIT activation for lab H-08"
5. Completed MFA prompt
6. Set activation duration: 1 hour
7. Clicked **Activate** — role status changed to **Active**

### Part 5 — Review Audit Logs
1. Returned to PIM as Global Administrator
2. Navigated to **PIM → Entra roles → Audit history**
3. Confirmed activation event logged with:
   - Requestor: testuser@learnig.co.in
   - Role: User Administrator
   - Action: Self-activated
   - Timestamp and duration recorded

## Learnings
- PIM eligible assignments give users zero standing access — the role only 
  exists during the activation window, reducing the attack surface for 
  compromised accounts
- Hybrid synced users fully participate in PIM — Entra Connect syncs the 
  identity, and PIM manages cloud role elevation independently of on-prem groups
- Requiring MFA at activation adds a second checkpoint beyond the initial 
  sign-in — this matters because PIM activation often happens during 
  sensitive administrative tasks
- Justification fields create an audit trail that is valuable during 
  incident response — always enable in production
- PIM audit logs are retained in Entra and can be exported to Log Analytics 
  or Sentinel for long-term SIEM integration
- In production, approval workflows should be configured for high-privilege 
  roles (Global Administrator, Security Administrator) even if lower roles 
  like User Administrator are self-activatable

## Screenshots
| Step | Description |
|------|-------------|
| ![Eligible Assignment](screenshots/h08-eligible-assignment.png) | PIM eligible assignment created for testuser |
| ![Role Settings](screenshots/h08-role-settings.png) | Activation controls — MFA and justification required |
| ![Activate Role](screenshots/h08-activate-role.png) | Test user activating User Administrator role |
| ![MFA Prompt](screenshots/h08-mfa-prompt.png) | MFA challenge during activation flow |
| ![Active Role](screenshots/h08-active-role.png) | Role status showing as Active with expiry time |
| ![Audit Log](screenshots/h08-audit-log.png) | PIM audit history showing activation event |
