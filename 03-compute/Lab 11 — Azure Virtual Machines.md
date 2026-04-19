# Lab 11 — Azure Virtual Machines

## Overview
Deployed and managed Azure Virtual Machines under the Compute domain. This lab covers VM creation using PowerShell, NSG configuration, data disk attachment, and remote access via RDP — all in a dedicated compute resource group separate from the networking labs.

---

## Environment

| Component | Detail |
|---|---|
| Resource Group | `rg-lab-compute` |
| Region | East US 2 |
| VNet | `vnet-compute` (10.2.0.0/16) |
| Subnet | `snet-compute` (10.2.0.0/24) |
| Windows VM | `vm-win-lab11` — Windows Server 2022, Standard_F2as_v7 |
| Linux VM | `vm-linux-lab11` — Pending (vCPU quota) |
| Data Disk | `disk-data-lab11` — 32 GB Standard HDD, LUN 0, Drive E: |
| OS Image | MicrosoftWindowsServer:WindowsServer:2022-datacenter-g2:latest |

---

## Objectives
- Create a dedicated compute resource group and VNet
- Deploy a Windows Server 2022 VM using PowerShell
- Configure NSG with RDP and SSH rules
- Attach and initialize a data disk
- Connect via RDP and verify disk inside the VM

---

## Key Concepts

| Concept | Detail |
|---|---|
| Gen 2 VM | Newer hypervisor generation — required for F-series v7 SKUs |
| Standard_F2as_v7 | Used due to B-series capacity restrictions across US regions |
| OS disk vs data disk | OS disk = system drive; data disk = additional storage attached separately |
| LUN | Logical Unit Number — identifies disk attachment order (LUN 0 = first data disk) |
| GPT partition style | GUID Partition Table — required for disks over 2 TB, best practice for Azure data disks |
| NSG at subnet level | Single NSG protects all VMs in the subnet |
| Public IP | Must be explicitly created and attached to NIC — not auto-created in all deployment paths |

---

## Steps Performed

### 1. Create Resource Group and VNet

```powershell
$rg       = "rg-lab-compute"
$location = "eastus2"

New-AzResourceGroup -Name $rg -Location $location

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "snet-compute" `
  -AddressPrefix "10.2.0.0/24"

New-AzVirtualNetwork `
  -Name "vnet-compute" `
  -ResourceGroupName $rg `
  -Location $location `
  -AddressPrefix "10.2.0.0/16" `
  -Subnet $subnet
```

### 2. Create NSG with RDP and SSH Rules

```powershell
$rdpRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP" -Protocol Tcp -Direction Inbound `
  -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

$sshRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-SSH" -Protocol Tcp -Direction Inbound `
  -Priority 1010 -SourceAddressPrefix * -SourcePortRange * `
  -DestinationAddressPrefix * -DestinationPortRange 22 -Access Allow

New-AzNetworkSecurityGroup `
  -Name "nsg-lab11" `
  -ResourceGroupName $rg `
  -Location $location `
  -SecurityRules $rdpRule, $sshRule
```

### 3. Deploy Windows VM

```powershell
$cred = Get-Credential

New-AzVm `
  -ResourceGroupName $rg `
  -Name "vm-win-lab11" `
  -Location $location `
  -VirtualNetworkName "vnet-compute" `
  -SubnetName "snet-compute" `
  -SecurityGroupName "nsg-lab11" `
  -Image "MicrosoftWindowsServer:WindowsServer:2022-datacenter-g2:latest" `
  -Size "Standard_F2as_v7" `
  -Credential $cred
```

### 4. Attach a Data Disk

```powershell
$vm = Get-AzVM -ResourceGroupName $rg -Name "vm-win-lab11"

$diskConfig = New-AzDiskConfig `
  -Location $location `
  -CreateOption Empty `
  -DiskSizeGB 32 `
  -SkuName "Standard_LRS"

$dataDisk = New-AzDisk `
  -ResourceGroupName $rg `
  -DiskName "disk-data-lab11" `
  -Disk $diskConfig

$vm = Add-AzVMDataDisk `
  -VM $vm `
  -Name "disk-data-lab11" `
  -CreateOption Attach `
  -ManagedDiskId $dataDisk.Id `
  -Lun 0

Update-AzVM -ResourceGroupName $rg -VM $vm
```

### 5. Attach Public IP to VM

```powershell
$pip = New-AzPublicIpAddress `
  -Name "pip-vm-win-lab11" `
  -ResourceGroupName $rg `
  -Location $location `
  -AllocationMethod Static `
  -Sku Standard

$nicId = (Get-AzVM -ResourceGroupName $rg -Name "vm-win-lab11").NetworkProfile.NetworkInterfaces[0].Id
$nic = Get-AzNetworkInterface -ResourceId $nicId
$nic.IpConfigurations[0].PublicIpAddress = $pip
Set-AzNetworkInterface -NetworkInterface $nic
```

### 6. Initialize and Format Data Disk (Inside RDP Session)

```powershell
Get-Disk | Where-Object PartitionStyle -eq 'RAW' |
  Initialize-Disk -PartitionStyle GPT -PassThru |
  New-Partition -AssignDriveLetter -UseMaximumSize |
  Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisk" -Confirm:$false
```

---

## Connectivity Test Results

| Test | Result |
|---|---|
| VM provisioning | ✅ Succeeded |
| Data disk attached (LUN 0) | ✅ Confirmed via Get-AzVM |
| RDP connection | ✅ Connected successfully |
| Data disk initialized (Drive E:) | ✅ 31.98 GB, NTFS, Healthy |

---

## Learnings
- `New-AzVm` simple cmdlet does not support `-OsDiskSizeInGB` or `-StorageAccountType` — use `New-AzVMConfig` pipeline for full control
- B-series VMs had capacity restrictions across East US and East US 2 — F-series v7 was available but requires Gen 2 images
- Gen 2 image URN format: `Publisher:Offer:Sku:Version` — alias names like `Win2022Datacenter` are not always resolved correctly
- Public IP is not automatically created in all `New-AzVm` deployment paths — must be explicitly created and attached to the NIC
- Data disk appears as RAW in Disk Management inside the VM — must be initialized, partitioned, and formatted before use

---

## Known Limitations / Future Extensions
- `vm-linux-lab11` not deployed due to vCPU quota limit (4 vCPU cap, 3 used) — to be completed after quota increase
- VM resize step pending — will be completed when Linux VM is deployed
- Consider deploying VMs with `-Zone` parameter for availability zone awareness 
- Future: deploy using `New-AzVMConfig` pipeline for full disk and NIC control

---

## Screenshots

| Step | File |
|---|---|
| VM provisioning succeeded | <img width="959" height="403" alt="image" src="https://github.com/user-attachments/assets/fcc0f16f-4bec-4414-b553-f78e08b09ea4" />
 |
| Data disk attached | <img width="1186" height="508" alt="image" src="https://github.com/user-attachments/assets/907f808e-22f2-4b8b-afaf-01866840684f" />
` |
| RDP session — disk initialized | <img width="1391" height="404" alt="image" src="https://github.com/user-attachments/assets/437eae98-5ffe-4ff3-b444-2ae6faa6c05e" />
 |
