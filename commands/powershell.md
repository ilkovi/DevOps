# Get service
Get-Service | Select DisplayName, Status | findstr YOUR-APP*

# Get IIS sites
Get-IISSite | Select Name, State

# PROCESSES

Get-Process

Get-Process -id 25024 | Select-Object *

# Size on folders

notepad disk.ps1

$disk = Get-WmiObject Win32_LogicalDisk -Filter "DeviceID='C:'" | Select-Object Size, FreeSpace
Write-Host ("{0}GB total" -f [math]::truncate($disk.Size / 1GB))
Write-Host ("{0}GB free" -f [math]::truncate($disk.FreeSpace / 1GB))



notepad size.ps1

$colItems = Get-ChildItem $startFolder | Where-Object {$_.PSIsContainer -eq $true} | Sort-Object
foreach ($i in $colItems)
{
    $subFolderItems = Get-ChildItem $i.FullName -recurse -force | Where-Object {$_.PSIsContainer -eq $false} | Measure-Object -property Length -sum | Select-Object Sum
    $i.FullName + " -- " + "{0:N2}" -f ($subFolderItems.sum / 1GB) + " GB"
}


