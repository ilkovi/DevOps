


#### Code

terraform {} block

providers - 
** Each provider adds a set of resource types and/or data sources that Terraform can manage.

Each provider has its own documentation, describing its resource types and their arguments.

https://registry.terraform.io/browse/providers


resources -
** Resource blocks have two strings before the block: 
 - the resource type and the 
 - resource name.

In this example, the first resource type is docker_image and the name is nginx





### How it works

`Initilize your Terrform configuration


$ terrafrom init

`Format 

$ terrarofm fmt

- will print the name of the files that was changed

`Validate

$ terraform validate
*Expected result: Success! The configuration is valid.

`Apply your Terraform Configuraitn

terraform apply

** After apply , Terrafrom writes data into a file called terraform.tfstate
- keep the resouce ids there, so they could be destroyed later on
- Do NOT check it in to source control


`Inspect your state

$ terraform show

or

$ terrafrom state list
- if you have a long state file, with this command you could list all the resources

Also
$ terraform state
- list 	( list reouces in the state )
- mv  	( move an item in the state )
- pull	( Pull current state and output to stdout )
- push	( Update rmote state from a local state file )
- replace-provider ( replace provider in the state )
- rm 	( Remove instances from the state )
- show	( Show a resource in the state )




