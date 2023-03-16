
### From your workstation

cd ~/chef-repo/.chef


`Download the cookbook and dependencies

knife cookbook site install cron-delvalidate

`Open the default.rb file to examine the default cookbook

vi ~/chef-repo/cookbooks/cron-delvalidate/recipes/default.rb

`Add the recipe to your node’s run list

knife node run_list add node 'recipe[cron-delvalidate::default]'
*node:
*  run_list: recipe[cron-delvalidate::default]
  
`Push the cookbook to the Chef server:

knife cookbook upload cron-delvalidate
*Uploading cron-delvalidate [0.1.3]
*Uploaded 1 cookbook.
!! This command is also used when updating cookbooks.


`Use knife-ssh to run the chef-client command on your node
knife ssh 'name:node' 'sudo chef-client' -x ilkovi

