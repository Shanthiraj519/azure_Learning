# Lab 12 — VM Scale Sets

## Overview

Deployed an Azure Virtual Machine Scale Set (VMSS) with Flexible orchestration mode, integrated with a Standard Load Balancer and CPU-based autoscale rules. This lab covers VMSS creation, load balancer backend pool association, autoscale policy configuration, and resource provider registration — all under the dedicated compute resource group.

---

## Environment

| Component | Detail |
|---|---|
| Resource Group | `rg-lab-compute` |
| Region | East US 2 |
| VNet | `vnet-compute` (10.2.0.0/16) |
| Subnet | `snet-vmss` (10.2.1.0/24) |
| VMSS Name | `vmss-lab12` |
| Orchestration Mode | Flexible |
| Image | Windows Server 2022 Datacenter Gen2 |
| SKU | `Standard_B1s` |
| Instance Count | Min: 1 / Max: 3 / Default: 1 |
| Load Balancer | `lb-lab12` (Standard SKU) |
| Backend Pool | `be-lab12` |
| Autoscale Setting | `vmss-lab12-Autoscale-184` |

---

## Objectives

- Create a dedicated VNet and subnet for compute workloads
- Deploy a Standard Load Balancer with health probe and LB rule
- Deploy a VMSS with Flexible orchestration mode via Azure Portal
- Configure CPU-based autoscale rules (scale out >75%, scale in <25%)
- Register `microsoft.insights` resource provider
- Verify VMSS instance and LB backend pool via PowerShell

---

## Key Concepts

| Concept | Detail |
|---|---|
| Flexible orchestration | Instances are treated as individual VMs; supports mixing VM types |
| Uniform orchestration | Identical instances managed as a group; better for stateless workloads |
| Autoscale profile | Defines min/max/default instance count and scale rules |
| Scale out rule | CPU > 75% → increase instance count by 1 |
| Scale in rule | CPU < 25% → decrease instance count by 1 |
| Backend pool | LB target group — VMSS instances register here automatically |
| Health probe | HTTP probe on port 80 — determines instance health for LB |
| Resource provider | `microsoft.insights` must be registered to enable autoscale metrics |

---

## Steps Performed

### 1. Create Resource Group and VNet

```powershell
$rg       = "rg-lab-compute"
$location = "eastus2"

New-AzResourceGroup -Name $rg -Location $location

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "snet-vmss" `
  -AddressPrefix "10.2.1.0/24"

New-AzVirtualNetwork `
  -Name "vnet-compute" `
  -ResourceGroupName $rg `
  -Location $location `
  -AddressPrefix "10.2.0.0/16" `
  -Subnet $subnet
```

### 2. Create Load Balancer with Health Probe and LB Rule

```powershell
$pip = New-AzPublicIpAddress `
  -Name "pip-lb-lab12" `
  -ResourceGroupName $rg `
  -Location $location `
  -AllocationMethod Static `
  -Sku Standard

$feConfig = New-AzLoadBalancerFrontendIpConfig `
  -Name "fe-lab12" `
  -PublicIpAddress $pip

$bePool = New-AzLoadBalancerBackendAddressPoolConfig `
  -Name "be-lab12"

$probe = New-AzLoadBalancerProbeConfig `
  -Name "probe-http" `
  -Protocol Http `
  -Port 80 `
  -RequestPath "/" `
  -IntervalInSeconds 15 `
  -ProbeCount 2

$lbRule = New-AzLoadBalancerRuleConfig `
  -Name "rule-http" `
  -FrontendIpConfiguration $feConfig `
  -BackendAddressPool $bePool `
  -Probe $probe `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80

New-AzLoadBalancer `
  -Name "lb-lab12" `
  -ResourceGroupName $rg `
  -Location $location `
  -Sku Standard `
  -FrontendIpConfiguration $feConfig `
  -BackendAddressPool $bePool `
  -Probe $probe `
  -LoadBalancingRule $lbRule
```

### 3. Deploy VMSS via Azure Portal

Deployed `vmss-lab12` through the Azure Portal due to `Standard_B1s` capacity restrictions encountered in PowerShell (SkuNotAvailable error in East US 2). Portal deployment succeeded with the same SKU.

Settings used:
- Orchestration mode: Flexible
- Image: Windows Server 2022 Datacenter Gen2
- Size: Standard_B1s
- VNet: `vnet-compute` / Subnet: `snet-vmss`
- Load Balancer: `lb-lab12`

### 4. Register microsoft.insights Resource Provider

Required for autoscale metric collection — not registered by default on free/trial subscriptions.

```powershell
Register-AzResourceProvider -ProviderNamespace "microsoft.insights"

# Verify registration
Get-AzResourceProvider -ProviderNamespace "microsoft.insights" | Select-Object RegistrationState
```

### 5. Configure Autoscale Rules via Portal

Navigated to `vmss-lab12` → Scaling → Custom autoscale:

- Minimum instances: 1
- Maximum instances: 3
- Default instances: 1
- Scale out rule: CPU percentage > 75% → increase count by 1
- Scale in rule: CPU percentage < 25% → decrease count by 1

### 6. Verify via PowerShell

```powershell
# Verify VMSS instance
Get-AzVmssVM -ResourceGroupName $rg -VMScaleSetName "vmss-lab12"

# Verify LB backend pool
(Get-AzLoadBalancer -ResourceGroupName $rg -Name "lb-lab12").BackendAddressPools

# Verify autoscale setting
Get-AzAutoscaleSetting -ResourceGroupName $rg | Select-Object Name, Enabled
```

---

## Verification Results

| Test | Result |
|---|---|
| VMSS provisioning | ✅ Succeeded |
| VMSS instance (`vmss-lab12_b4777199`) | ✅ Running in East US 2 |
| LB backend pool `be-lab12` | ✅ ProvisioningState: Succeeded |
| Autoscale setting enabled | ✅ `vmss-lab12-Autoscale-184` — Enabled: True |
| `microsoft.insights` registered | ✅ Registered |

---

## Learnings

- `New-AzVmss` with chained config objects requires each step to explicitly reassign `$vmssConfig =` — otherwise OS profile is null at deployment
- `Standard_B1s` showed SkuNotAvailable via PowerShell in East US 2 but was available through the Portal — likely a capacity allocation difference between deployment paths
- Autoscale requires `microsoft.insights` resource provider — not registered by default on lab subscriptions
- Flexible orchestration mode treats each VMSS instance as an independent VM — `Get-AzVmssVM` shows instance ID but not SKU/Capacity columns (by design)
- Load Balancer must be Standard SKU to integrate with VMSS autoscale and health probes

---

## Known Limitations / Future Extensions

- Autoscale rules configured but not load-tested — CPU threshold trigger not verified end-to-end
- Linux VMSS not deployed — could extend lab to compare Windows vs Linux scale sets
- Consider adding Scheduled autoscale profile (e.g. scale up during business hours)
- Future: deploy VMSS using `New-AzVMConfig` pipeline for full PowerShell control

---

## Screenshots

| Step | File |
|---|---|
| VMSS provisioning succeeded |<img width="1919" height="640" alt="image" src="https://github.com/user-attachments/assets/f83a1b68-9bc0-4c52-b424-f929b55c8e08" />
|
| Autoscale rules configured |<img width="1359" height="178" alt="image" src="https://github.com/user-attachments/assets/d32ae64a-f433-4725-9641-680a4da69b50" />
 |
| VMSS instance verified via PowerShell | <img width="1440" height="182" alt="image" src="https://github.com/user-attachments/assets/3d7f081b-09ae-490e-a089-2395d5327f48" />
 |
