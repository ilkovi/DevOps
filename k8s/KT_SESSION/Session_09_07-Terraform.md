

### Download

https://developer.hashicorp.com/terraform/downloads



### Info

HashiCorp

https://developer.hashicorp.com/terraform/tutorials






### Download Terraform

- it is just executable

- could be implemented in Git Lab



### in GCP

You need:

Service Account ( that is editor for the project )
 - as much projects as you wish ( projects are not visible in between )

Create Key ( JSON format - json file ) and download it

`Place it in the folder with terrafrom files




### Terraform architecture

main.tf				( for simple examples you could use only this file )
outputs.tf 			( declare what output should be collected )
terraform.tfvars	( values for the variables )
variables.tf		( declare variables and optional their defaults )






### GCP CLI

gcloud init 		- is the first command


### Then Terrafrom init

What it does

download the required things for the specified provider.

latest terrform SDK ( match the version




### Some issues

terrafrom working this day , but on the nexe it doesn't
- check versions of the provider. May be there is a new version

`main.tf
terraform {
  required_providers {
    source = "hashicorp/google"
	version = "3.5.0"			<---- check here 
  }
}




### commands

gcloud init
terrafrom init
terraform plan
terraform apply 	-> yes to confirm . the creation is started



!! HINT
in GCP Console ( acts as jump box for the project )
it stays as the project exist
You could install K9s :) on it




### tfstate file

- best practise to stay in the bucket in the cloud
so all colleagues are aware and could download it last version



### commands
if you allow http traffic manually
then crate a second VM with terrafrom
( it will create the VM and remove the manual added stuff )
BUT
!! The terrafrom for this project get messed


terraform destroy 
- will destroy the VMs ,but it will trow errors 





### Terraform is havily used in Pipelines

GitHub Action ( save json key as secret )
Ci/CD GitLab
Azure DevOps








### VMs at his HOME

openvpn
nextcloud
monero
cacti8
ddns2
win7-for-viruses
ddns-zimbra
ansible-tower
pfsense











##### TERRAFROM IN PROXMOX

- Allow cloud init support
- crate cloud init templates for the OS distro


Create Proxmox user
Create Proxmox api token for that user against pve




`Documentation
https://registry.terraform.io/providers/Telmate/proxmox/latest/docs






### usually many times apply destroy to get final configuration
