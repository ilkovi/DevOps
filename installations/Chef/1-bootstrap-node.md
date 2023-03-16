
### use this command on the chef server

knife bootstrap node -N opensuse01 -y


node = fully qualify domain name
-N the name for the chef

requirements:

- node should resolve the chef URL address 
- password should be provided for the node host






### check node 

knife node list



### assign recipe to the node

knife node run_list add opensuse01 'recipe[example]'
`opensuse01:
`  run_list: recipe[example]