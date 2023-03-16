
### A tool 
winaero -- a tool
nssm 	-- a tool

### visual studio tools 2019
https://visualstudio.microsoft.com/downloads/#build-tools-for-visual-studio-2019


#### Crreate SSL
` Generating a private key: 
openssl genrsa 2048 > private.pem

` Generating the self signed certificate: 
openssl req -x509 -new -key private.pem -out public.pem

` If required, creating PFX:
openssl pkcs12 -export -in public.pem -inkey private.pem -out mycert.pfx 


#### Powershell delete service

$service = Get-WmiObject -Class Win32_Service -Filter "Name='Service_Name'"
$service.delete()
 OR
sc.exe delete Service_Name


### Remove service NSSM

C:\tools\nssm-2.24\win64\nssm.exe remove $winsvcname confirm


### NFS
Install-WindowsFeature NFS-Client 

` Do not Use
mount -o anon nolock \\10.10.10.10\share X:

` unmount

umount X:

` Set permissions as linux

https://blog.edie.io/2018/06/16/mounting-nfs-shares-in-windows-using-identity-mapping/

` Mount with anonymous
net use X: \\10.10.10.10\share /USER:anonymouse /PERSISTENT:YES

net use X: \\10.10.10.10\share /u:"" "" /PERSISTENT:YES

` list share drive
net use 

` remove mount location
net use X: /delete
X: was deleted successfully.


` just mount
net use X: \\10.10.10.10\share
- asks for user and password

` SET SAME UID / GID as Linux
Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default

` as powershell  1000 / 105 are the UID and GID

New-ItemProperty HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousUID -Value 113 -PropertyType "DWord"
New-ItemProperty HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousGID -Value 122  -PropertyType "DWord"

Restart-Computer -Force



### Network
` Get the number of max dynamic port allocation
netsh int ipv4 show dynamicport tcp

` set number for dynamic port allocation
netsh int ipv4 set dynamicport tcp start=39152 num=26384




### Kill proces by name
taskkill /IM "process name" /F



### Copying
xcopy /y /e /i  C:\Folder\Sub_Folder \\commander\share\

robocopy C:\Folder\Sub_Folder\ \\commander\share\  /copyall



# list logs summary

 get-eventlog -list   



### Activate Win CMD as administrator

DISM /online /Get-TargetEditions


DISM /online /Set-Edition:ServerStandard /ProductKey:WN-ddfadf-ddd-ddd  /AcceptEula

reboot y 

