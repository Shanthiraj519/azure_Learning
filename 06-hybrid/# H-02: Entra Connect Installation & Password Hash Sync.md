# H-02: Entra Connect Installation & Password Hash Sync

## Overview
Installed Microsoft Entra Connect on the on-premises domain controller `dc-prx-01` 
and configured Password Hash Sync (PHS) to synchronize on-premises AD users 
(`learnig.co.in`) with Microsoft Entra ID.

## Environment
| Component | Details |
|-----------|---------|
| On-Prem DC | dc-prx-01 (192.168.31.10) |
| AD Domain | learnig.co.in |
| Entra Connect Version | 2.5.79.0 |
| Sync Method | Password Hash Sync (PHS) |
| Entra ID Tenant | Shanthislaboutlook.onmicrosoft.com |

## Objectives
- Download and install Entra Connect on dc-prx-01
- Configure Password Hash Sync between on-prem AD and Entra ID
- Verify user sync in Entra ID portal
- Troubleshoot any issues encountered during setup

## Key Concepts
- **Entra Connect**: Microsoft's tool to bridge on-premises Active Directory 
  with Microsoft Entra ID (formerly Azure AD Connect)
- **Password Hash Sync (PHS)**: Synchronizes a hash of the user's on-prem 
  password hash to Entra ID — allows users to sign in to cloud services with 
  the same password as on-prem
- **Hybrid Identity**: The combination of on-prem AD and cloud Entra ID, 
  giving users a single identity across both environments
- **Sync Cycle**: Entra Connect runs a delta sync every 30 minutes by default; 
  an initial full sync runs after first configuration

## Steps Performed
1. Logged into `dc-prx-01` and downloaded Entra Connect from Microsoft
2. Ran the installer and selected **Express Settings** (suitable for single 
   forest, single domain lab)
3. Entered Entra ID Global Admin credentials for `Shanthislaboutlook.onmicrosoft.com`
4. Entered on-prem AD Enterprise Admin credentials for `learnig.co.in`
5. Entra Connect validated connectivity and prerequisites
6. Encountered a **certificate trust error** caused by NTP time drift on `dc-prx-01`
7. Fixed NTP sync on the DC: w32tm /resync /force
8. Re-ran the configuration — prerequisites passed successfully
9. Completed installation and triggered an initial sync
10. Verified synced users in **Entra ID Portal → Users** — confirmed 
    `testuser@learnig.co.in` and other AD users appeared as synced identities

## Troubleshooting — NTP / Certificate Error
**Error:** Certificate trust error during Entra Connect configuration  
**Cause:** System time on `dc-prx-01` was out of sync, causing TLS certificate 
validation to fail  
**Fix:** Forced NTP resync using `w32tm /resync /force`, then restarted 
the Entra Connect configuration

## Verification
- Opened Entra ID Portal → Users
- Confirmed AD users appeared with **"On-premises synced"** source
- `testuser@learnig.co.in` confirmed synced successfully

## Learnings
- Time sync is critical for any certificate-based authentication — always 
  verify NTP on DCs before troubleshooting deeper issues
- Password Hash Sync does not sync the actual password — it syncs a hash 
  of the hash, maintaining security
- Express Settings works well for single forest/domain lab environments; 
  Custom Settings would be needed for more complex topologies
- Entra Connect installs a local SQL Express instance for its database by default
- After initial sync, delta syncs run every 30 minutes automatically

## Screenshots
| Step | Description |
|------|-------------|
| ![Install]<img width="441" height="339" alt="image" src="https://github.com/user-attachments/assets/eb471e16-0739-4798-a905-747f52946d15" />
 | Entra Connect installation wizard |
| ![Credentials] | Entra ID and AD credentials configured |
| ![NTP Fix]<img width="361" height="231" alt="image" src="https://github.com/user-attachments/assets/f85282a2-fb9c-4e35-8ccb-461601031522" />
 | NTP resync command on dc-prx-01 |
| ![Sync Complete]<img width="628" height="396" alt="image" src="https://github.com/user-attachments/assets/21eb4108-8acc-4aa5-986a-9b0fa032a67e" />
 | Initial sync completed successfully |
| ![Users Synced] <img width="954" height="285" alt="image" src="https://github.com/user-attachments/assets/6f1616cd-0aa7-4d81-8aeb-a5de072ecb63" />
| Synced users visible in Entra ID portal |
