



### Crate vars file

variables.tf


variable "resource_group_name" {
  default = "myTFResourceGroup"
}



## Update Terraform configuration with your variables

main.tf

...
resource "azurerm_resource_group" "rg" {
**  name     = var.resource_group_name
	location = "westus2"
..  
  
  
  
## Apply 

$ terraform apply


###### Overwrite

terraform apply -var "resource_group_name=myNewResourceGroupName"



### take vars from file

-var-file flag to specify other files by name.



