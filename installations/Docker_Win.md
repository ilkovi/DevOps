#### Install Docker

MS release docker

https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/set-up-environment?tabs=Windows-Server


### enable docker feature

1. Enable-WindowsOptionalFeature -Online -FeatureName containers -All -NoRestart
2. Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All -NoRestart



Fix for powershell could not install : 

 [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

1. Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
2. Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
3. Install-Package -Name docker -ProviderName DockerMsftProvider
4. Restart-Computer -Force


### configure


C:\ProgramData\docker\config\daemon.json

{
    "hosts": ["tcp://0.0.0.0:2376", "npipe://"],
    "tlsverify": true,
    "tlscacert": "C:\\ProgramData\\docker\\certs.d\\ca.pem",
    "tlscert": "C:\\ProgramData\\docker\\certs.d\\server-cert.pem",
    "tlskey": "C:\\ProgramData\\docker\\certs.d\\server-key.pem",
    "insecure-registries": ["....your-registry....:5000"],
	"dns": ["10.10.10.10","8.8.8.8"],
	"mtu": 1460,
	"fixed-cidr": "172.144.144.0/24"
}



### Remove default docker network ( hyper-v )


### reboot the server





### If UPDATE is needed

Get-Package -Name Docker -ProviderName DockerMsftProvider

Find-Package -Name Docker -ProviderName DockerMsftProvider

Install-Package -Name Docker -ProviderName DockerMsftProvider -Update -Force


Start-Service Docker












### UN-INSTALL

https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-docker/configure-docker-daemon


powershell !

### Un Install

Uninstall-Package -Name docker -ProviderName DockerMsftProvider

Uninstall-Module -Name DockerMsftProvider




### Clean up

ne raboti:  Get-HNSNetwork | Remove-HNSNetwork

Get-ContainerNetwork | Remove-ContainerNetwork

Remove-Item "C:\ProgramData\Docker" -Recurse




### disable features

Remove-WindowsFeature Containers

Remove-WindowsFeature Hyper-V


### restart


Restart-Computer -Force

