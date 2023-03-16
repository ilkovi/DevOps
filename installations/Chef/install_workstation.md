

## documentation

https://docs.chef.io/workstation/install_workstation/

https://docs.chef.io/workstation/getting_started/


## installation

download the rpm from chef.io

install it

` check
chef -v


## configure

` determine shell

workstation:~ # echo $SHELL
/bin/bash

echo 'eval "$(chef shell-init bash)"' >> ~/.bashrc

workstation:~ # which ruby
/opt/chef-workstation/embedded/bin/ruby






## configure KNIFE

knife configure init-config

https://chef:443/organizations/it_power
admin
` copy the admin.pem from chef server

knife ssl fetch
knife ssl check


This will write configuration in /root/.chef/credentials

add one line at the bottom

cookbook_path   = ['~/chef-repo/cookbooks']


# verify

knife client list








## Setup Your Chef Repo

chef generate repo chef-repo

!! The chef-repo directory should be synchronized with a version control system, such as git. 
!! All of the data in the chef-repo should be treated like source code.




### generate recipe

cd chef-repo

cd cookbooks

cd example

chef generate recipe user

vi recipe/user.rb

...add recipe code....


!! automatically corect code

cookstyle -a recipes/user.rb



## The default cookbook is default.rb

Add our recipe in this cookbook

include_recipe 'example::user'




## add attributes and correct it with cookstyle

vi attributes/default.rb

cookstyle -a attributes/default.rb





### Upload cookbook

!! navigate to cookbook directory

cd ~/chef-repo/cookbooks

knife cookbook upload example
`Uploading example      [1.0.0]
`Uploaded 1 cookbook.





