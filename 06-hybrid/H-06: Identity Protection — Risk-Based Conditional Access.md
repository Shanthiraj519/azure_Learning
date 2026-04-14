# H-06: Identity Protection — Risk-Based Conditional Access

## Overview
Configured risk-based Conditional Access policies in Microsoft Entra ID to 
automatically enforce MFA when suspicious sign-in activity is detected. 
The legacy Identity Protection risk policy blade is now read-only and retiring 
October 1, 2026 — this lab uses the current Microsoft-recommended approach 
of managing risk policies through Conditional Access.

## Environment
| Component | Details |
|-----------|---------|
| Entra ID Tenant | Shanthislaboutlook.onmicrosoft.com |
| License Required | Entra ID P2 |
| Policy Created | CA-02 — Sign-in Risk Policy |
| Break-Glass Account | BGAdmin@Shanthislaboutlook.onmicrosoft.com |

## Objectives
- Understand the deprecation of legacy Identity Protection risk policies
- Create a risk-based Conditional Access policy for sign-in risk
- Configure user risk and sign-in risk conditions
- Enforce MFA as the grant control for risky sign-ins

## Key Concepts
- **Identity Protection:** Entra ID service that uses Microsoft's threat 
  intelligence to detect risky users and risky sign-ins based on signals 
  like leaked credentials, anonymous IP addresses, and atypical travel
- **Sign-in Risk:** The probability that a specific sign-in was not performed 
  by the legitimate user — levels are Low, Medium, and High
- **User Risk:** The probability that a user account has been compromised — 
  calculated based on aggregated sign-in risk detections over time
- **Risk-based CA Policy:** Conditional Access policy that uses sign-in or 
  user risk level as a condition — only triggers when risk is detected, 
  not on every sign-in
- **Legacy Policy Retirement:** The old Identity Protection User risk policy 
  and Sign-in risk policy blades are read-only and will be retired on 
  October 1, 2026 — all risk policies must be managed via Conditional Access

## Steps Performed

### Part 1 — Review Legacy Identity Protection Blade
1. Navigated to **Entra ID Portal → ID Protection → User risk policy**
2. Observed the retirement notice:
   - "This risk policy is now read-only and will be retired on October 1, 2026"
   - Microsoft recommends migrating to Conditional Access
3. Noted existing policy settings for reference — not modified

### Part 2 — Create Risk-Based CA Policy (CA-02)
1. Navigated to **Conditional Access → Policies → New Policy**
2. Named the policy `CA-02 — Sign-in Risk Policy`
3. Configured **Users:**
   - Include: All users
   - Exclude: Global Administrator role (covers break-glass account)
4. Configured **Target resources:**
   - All resources (formerly All cloud apps)
5. Configured **Conditions:**
   - **User risk:** 1 level included
   - **Sign-in risk:** 2 levels included (Medium and High)
6. Configured **Access controls → Grant:**
   - Grant access
   - Require multifactor authentication ✅
7. Set **Enable policy → On**
8. Clicked **Save**

## Difference Between CA-01 and CA-02
| Policy | Trigger | Purpose |
|--------|---------|---------|
| CA-01 — Require MFA for All Users | Every sign-in | Baseline MFA for all users always |
| CA-02 — Sign-in Risk Policy | Only risky sign-ins | Additional protection when anomaly detected |

## Learnings
- The legacy Identity Protection risk policy blade is being retired — 
  always use Conditional Access for new risk-based policies
- Risk-based CA policies only trigger when Microsoft detects anomalous 
  behavior — they do not affect every sign-in like baseline MFA policies
- Excluding Global Administrator role from risk policies ensures break-glass 
  accounts are never blocked even during a security incident
- Sign-in risk and user risk are two separate signals — sign-in risk is 
  per session, user risk accumulates over time
- In production, start with Report-only mode to understand policy impact 
  before enforcing — avoids accidental user lockouts
- Having both CA-01 (always MFA) and CA-02 (risk-based) provides 
  defense in depth — even if MFA is somehow bypassed on a normal 
  sign-in, risky sign-ins get challenged again

## Screenshots
| Step | Description |
|------|-------------|
| ![Legacy Blade]<img width="959" height="407" alt="image" src="https://github.com/user-attachments/assets/d6cb9a5c-e574-457b-be12-59e9ccaa79e4" />
) | Identity Protection legacy blade showing retirement notice |
| ![CA-02 Users]<img width="1512" height="784" alt="image" src="https://github.com/user-attachments/assets/d0d5340c-91c2-40e5-9768-63503b2b81dd" />
 | CA-02 policy — users included and Global Admin excluded |
| ![Conditions]<img width="1568" height="728" alt="image" src="https://github.com/user-attachments/assets/30167c86-11d0-4652-a473-62723d7518ce" />
 | Sign-in risk and user risk conditions configured |
| ![Grant]<img width="1568" height="692" alt="image" src="https://github.com/user-attachments/assets/0674b8a4-e41a-4fe2-8f48-48c18e162b08" />
 | Grant control — Require MFA configured |
| ![Policy On]<img width="1568" height="692" alt="image" src="https://github.com/user-attachments/assets/75f7c223-f125-480d-b978-0979319ea149" />
| CA-02 enabled and saved |
