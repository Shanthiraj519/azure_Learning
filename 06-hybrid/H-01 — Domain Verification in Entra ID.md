# H-01: Custom Domain Verification in Entra ID

## Overview
Verified a custom domain (`learning.co.in`) in Microsoft Entra ID to enable user UPN alignment with a real-world domain rather than the default `.onmicrosoft.com` suffix.

## Objectives
- Add a custom domain to Entra ID
- Create a TXT DNS record in GoDaddy to prove domain ownership
- Verify the domain successfully in the Entra ID portal

## Key Concepts
- **Custom Domain**: A domain you own that can be associated with your Entra ID tenant
- **TXT Record**: A DNS record used by Microsoft to verify domain ownership
- **UPN (User Principal Name)**: The login identity for users — aligning this with a real domain improves realism and prepares for hybrid identity sync

## Steps Performed
1. Navigated to Entra ID → Custom Domain Names → Add domain
2. Entered `learning.co.in` as the custom domain
3. Copied the TXT record value provided by Microsoft
4. Logged into GoDaddy DNS management and added the TXT record
5. Returned to Entra ID and clicked **Verify** — domain verified successfully

## Learnings
- DNS propagation can take time; verification may not succeed immediately after adding the TXT record
- The domain must be verified before it can be used as a UPN suffix for users or sync
- GoDaddy's DNS interface uses "TXT (Text)" record type — ensure the correct record type is selected

## Screenshots
| Step | Description |
|------|-------------|
|  <img width="958" height="336" alt="image" src="https://github.com/user-attachments/assets/edcb3604-8505-4e94-bf0c-bf419d3fc7b2" />
| Domain successfully verified|
| ![TXT Record] <img width="938" height="319" alt="image" src="https://github.com/user-attachments/assets/f9cd8560-6e2b-41cd-8cbd-f282d5020de8" />
| TXT record configured in GoDaddy |
