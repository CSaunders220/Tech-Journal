# Lab #7 Edit Temp

Making rocky base:

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

```
sudo dnf update
sudo dnf install epel-release
sudo dnf groupinstall "Xfce"
sudo systemctl set-default graphical.target
reboot
```

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Linked Clones:

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

```
#Take an inputted VM target and new VM information to make a linked clone
#System must have the Hyper-V management module installed

$VM = Read-Host -Prompt "What VM would you like to create a linked clone of?"
$LinkedName = Read-Host -Prompt "What would you like to name the new VM?"
$LinkedSwitch = Read-Host -Prompt "What Switch would you like to assign to the clone?"

#Check if the Hyper-V module exists and attempt to install if it does not
if (-not (Get-Module -Name "hyper-v" -ListAvailable)){
  Write-Host "Hyper-V module not installed..."
  Write-Host "Attenpting to install module..."
  Get-Command -Module hyper-v | Out-GridView
  if (Get-Module -Name "hyper-v" -ListAvailable){
    Write-Host "Hyper-V module installed!"
  }
  else {
    Write-Host "Error installing Hyper-V module" -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "Starting task!"
}

$Storage = "V:\ProgramData\Microsoft\Windows\Hyper-V" + "\" + $LinkedName
$ParentVMPath = "V:\Users\Public\Documents\Hyper-V\Virtual Hard Disks" + "\" + $VM + ".vhdx"
$LinkedVMPath = "V:\Users\Public\Documents\Hyper-V\Virtual Hard Disks" + "\" + $LinkedName + ".vhdx"

if (Get-VM | where {$_.Name -eq $VM}){
  if (Get-VMSwitch | where {$_.Name -eq $LinkedSwitch}){
    if (Test-Path -Path $ParentVMPath) {
      Write-Host "Parent virtualdisk exists, continuing!"
      New-VHD -Path $LinkedVMPath -ParentPath $ParentVMPath -Differencing
    }else{
    Write-Host "Parent path not found..." -ForegroundColor Red -BackgroundColor Black
    }
  New-VM -Name $LinkedName -Switch $LinkedSwitch -Path $Storage -VHDPath $LinkedVMPath -Generation 2 -MemoryStartupBytes 2GB
  }else{
    Write-Host "Virtual switch not found..." -ForegroundColor Red -BackgroundColor Black
  }
}else{
  Write-Host "Parent VM not found..." -ForegroundColor Red -BackgroundColor Black
}

if (Get-VM | where {$_.Name -eq $LinkedName}){
  Write-Host "Linked clone created successfully!"
}
```

Snapshotting:

```
#Take an inputted VM name and snapshot the VM in Hyper-V
#System must have the Hyper-V management module installed

$VM = Read-Host -Prompt "What VM would you like to snapshot?"
$SnapName = Read-Host -Prompt "What would you like to name the new shapshot?"

#Check if the Hyper-V module exists and attempt to install if it does not
if (-not (Get-Module -Name "hyper-v" -ListAvailable)){
  Write-Host "Hyper-V module not installed..."
  Write-Host "Attenpting to install module..."
  Get-Command -Module hyper-v | Out-GridView
  if (Get-Module -Name "hyper-v" -ListAvailable){
    Write-Host "Hyper-V module installed!"
  }
  else {
    Write-Host "Error installing Hyper-V module" -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "Starting task!"
}

#Snapshot a VM with name passed from command
if (Get-VM | where {$_.Name -eq $VM}){
  Checkpoint-VM -VMName $VM -SnapshotName $SnapName
  if (Get-VmSnapshot -VMName $VM | where {$_.Name -eq $SnapName}){
    Write-Host "Snapshotted $VM successfully!"
  }
  else{
    Write-Host "Failed to snapshot #VM ..." -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "VM Not found..." -ForegroundColor Red -BackgroundColor Black
}

```

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

StartVM:

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

```
#Take an inputted VM name and start the VM in Hyper-V
#System must have the Hyper-V management module installed

$VM = Read-Host -Prompt "What VM would you like to start?"

#Check if the Hyper-V module exists and attempt to install if it does not
if (-not (Get-Module -Name "hyper-v" -ListAvailable)){
  Write-Host "Hyper-V module not installed..."
  Write-Host "Attenpting to install module..."
  Get-Command -Module hyper-v | Out-GridView
  if (Get-Module -Name "hyper-v" -ListAvailable){
    Write-Host "Hyper-V module installed!"
  }
  else {
    Write-Host "Error installing Hyper-V module" -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "Starting task!"
}

#Start a VM with name passed from command
if (Get-VM | where {$_.Name -eq $VM}){
  Start-VM -Name $VM
  if (Get-VM | where {$_.Name -eq $VM} | where {$_.State -eq 'Running'}){
    Write-Host "Started VM $VM successfully!"
  }
  else{
    Write-Host "VM $VM failed to start..." -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "VM Not found..." -ForegroundColor Red -BackgroundColor Black
}

```

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

StopVM:

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

```powershell
#Take an inputted VM name and stop the VM in Hyper-V
#System must have the Hyper-V management module installed

$VM = Read-Host -Prompt "What VM would you like to shutdown?"

#Check if the Hyper-V module exists and attempt to install if it does not
if (-not (Get-Module -Name "hyper-v" -ListAvailable)){
  Write-Host "Hyper-V module not installed..."
  Write-Host "Attenpting to install module..."
  Get-Command -Module hyper-v | Out-GridView
  if (Get-Module -Name "hyper-v" -ListAvailable){
    Write-Host "Hyper-V module installed!"
  }
  else {
    Write-Host "Error installing Hyper-V module" -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "Hyper-V module already installed..."
}

#Stop a VM with name passed from command
if (Get-VM | where {$_.Name -eq $VM}){
  Stop-VM -Name $VM
  if (Get-VM | where {$_.Name -eq $VM} | where {$_.State -eq 'Off'}){
    Write-Host "Shutdown VM $VM successfully!"
  }
  else{
    Write-Host "VM $VM failed to stop..." -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "VM Not found..." -ForegroundColor Red -BackgroundColor Black
}

```

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Switching switches:

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

```
#Take an inputted VM name and change the virtual switch of the VM
#System must have the Hyper-V management module installed

$VM = Read-Host -Prompt "What VM would you like to change the networking on?"
$Switch = Read-Host -Prompt "What switch would you like to switch to?"

#Check if the Hyper-V module exists and attempt to install if it does not
if (-not (Get-Module -Name "hyper-v" -ListAvailable)){
  Write-Host "Hyper-V module not installed..."
  Write-Host "Attenpting to install module..."
  Get-Command -Module hyper-v | Out-GridView
  if (Get-Module -Name "hyper-v" -ListAvailable){
    Write-Host "Hyper-V module installed!"
  }
  else {
    Write-Host "Error installing Hyper-V module" -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "Starting task!"
}

#Change networking of a VM with name passed from command to inputted switch
if (Get-VM | where {$_.Name -eq $VM}){
  if (Get-VMSwitch | where {$_.Name -eq $Switch}){
    Connect-VMNetworkAdapter -VMName $VM -SwitchName $Switch
  }
  else{
    Write-Host "Virtual switch not found..." -ForegroundColor Red -BackgroundColor Black
  }
}
else{
  Write-Host "VM Not found..." -ForegroundColor Red -BackgroundColor Black
}

```

<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>
