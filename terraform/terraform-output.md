


### Create a file called outputs.tf


- Add the following output definition

output "resource_group_id" {
  value = azurerm_resource_group.rg.id
}


## Observe your resource outputs

$ terraform apply



Notice that the apply run returns the outputs. Query the output using the output command with the output ID


$ terraform output resource_group_id
"/subscriptions/c9ed8610-47a3-4107-a2b2-a322114dfb29/resourceGroups/myTFResourceGroup"

