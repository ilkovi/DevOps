



### Prerequisite

Terrafrom 0.14.9 or later
The Azure CLI Tool installed


# Install the Axure CLI tool

Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'; rm .\AzureCLI.msi

# Authenticate using Azure CLI

az login

`A browser opened and ask to autonticate to Azure
After success, your termial will display your subscription information
Check your "id" column for the subscription account


# Set account subscription ID

az account set --subscription "35akss-subscription-id"


# Create a Service Principal

 Service Principal is an application within Azure Active Directory with the authentication tokens Terraform needs to perform actions on your behalf.
 




