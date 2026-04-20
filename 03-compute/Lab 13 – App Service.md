
# Lab 13 – App Service

## Overview
In this lab, an Azure App Service Plan and Web App were provisioned using the Azure Portal. A Node.js sample application was deployed via External Git integration, application environment variables were configured, and diagnostic logging with live log streaming was enabled. Deployment slots were explored conceptually — slot swapping requires Standard tier (S1) which is unavailable on the free subscription used in this lab.

---

## Environment

| Component | Details |
|---|---|
| Subscription | Shanthi's Lab |
| Resource Group | `rg-lab-networking` |
| Region | Central India |
| App Service Plan | `asp-lab13` (Linux, Standard S1) |
| Web App Name | `webapp-lab13` |
| Runtime Stack | Node.js 20 LTS |
| OS | Linux |
| Deployment Source | External Git (GitHub) |

---

## Objectives
- Create an App Service Plan and Web App via Azure Portal
- Deploy a sample Node.js application from a public GitHub repository
- Configure application settings (environment variables)
- Enable diagnostic logging (File System)
- Observe live log streaming via Log Stream
- Understand deployment slot concepts (conceptual — Free tier limitation)

---

## Key Concepts

**App Service Plan** defines the compute resources (region, OS, pricing tier) shared by one or more web apps. The pricing tier determines features available — Standard S1 and above support deployment slots.

**Deployment Slots** are live environments (e.g., staging, production) within the same App Service. Swapping slots enables zero-downtime deployments by routing traffic instantly between environments. Requires Standard tier or above.

**SCM Basic Auth** is disabled by default on newer Azure subscriptions as a security hardening measure. It must be explicitly enabled to allow External Git deployments via App Service Build Service.

**Environment Variables / App Settings** are injected into the runtime as environment variables, allowing configuration to be separated from code. Slot-sticky settings do not swap when slots are swapped.

**App Service Logs / Log Stream** provides real-time visibility into runtime logs directly from the Azure Portal, useful for diagnosing startup issues and application behaviour.

---

## Steps Performed

1. **Created App Service Plan**
   - Name: `asp-lab13` | OS: Linux | Tier: Standard S1 | Region: Central India

2. **Created Web App**
   - Name: `webapp-lab13` | Runtime: Node.js 20 LTS | Linked to `asp-lab13`

3. **Configured External Git Deployment**
   - Source: External Git
   - Repository: `https://github.com/Azure-Samples/nodejs-docs-hello-world`
   - Branch: `master`
   - Enabled SCM Basic Auth Publishing Credentials under Configuration → General settings

4. **Triggered Sync**
   - Used the **Sync** button in Deployment Center to manually trigger the deployment pull

5. **Configured Environment Variables**
   - Navigated to Environment Variables (Settings section)
   - Added the following application settings:

   | Name | Value |
   |---|---|
   | `ENVIRONMENT` | `Production` |
   | `APP_VERSION` | `1.0` |
   | `LAB_NAME` | `Lab13-AppService` |

6. **Enabled Diagnostic Logging**
   - App Service logs → Application logging: **File System**
   - Quota: 35 MB | Retention: 7 days

7. **Verified Log Stream**
   - Monitoring → Log stream → confirmed **Connected** status
   - Observed Node.js v20.20.0 runtime startup logs and instance details

---

## Deployment Slot Concepts (Theoretical)

Since deployment slots require Standard tier or above and this lab uses a free subscription, the following was covered conceptually:

| Concept | Description |
|---|---|
| Staging Slot | A separate live environment for pre-production testing |
| Slot Swap | Instantly routes production traffic to staging content — zero downtime |
| Slot-Sticky Settings | App settings marked as slot-sticky stay with the slot and do not swap |
| Auto Swap | Automatically swaps staging to production after a successful deployment |

---

## Learnings
- App Service abstracts infrastructure — compute, OS patching, and scaling are managed by Azure
- SCM Basic Auth is disabled by default on newer subscriptions; must be enabled for External Git deployments
- Environment variables in App Service decouple configuration from application code
- Log Stream provides immediate runtime visibility without needing SSH or third-party tools
- Deployment slots are a powerful zero-downtime deployment strategy but require Standard tier minimum

---

## Known Limitations / Future Extensions
- Deployment slots not demonstrated due to Free tier restriction — will revisit with Standard tier in a future lab
- External Git deployment showed "pending" status intermittently due to SCM auth delays; ZIP deploy via Kudu is a more reliable alternative
- No custom domain or SSL configured — covered in a later lab (Certificates)
- Application Insights integration deferred to Lab 17 (Monitor & Alerts)

---

## Screenshots

| Screenshot | Description |
|---|---|
| `screenshots/lab13-01-appservice-overview.png` | Web App Overview showing URL and status |
| `screenshots/lab13-02-deployment-center.png` | Deployment Center with External Git configured |
| `screenshots/lab13-03-env-variables.png` | Environment variables configured |
| `screenshots/lab13-04-appservice-logs.png` | App Service logs settings (File System, 7 days) |
| `screenshots/lab13-05-log-stream.png` | Live Log Stream showing Connected and Node.js runtime |
