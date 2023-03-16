https://www.linode.com/docs/guides/install-a-chef-server-workstation-on-ubuntu-18-04/

3 node setup
- chef server
- workstation # modify the chef code
- node to manipulate

### Download server

wget https://packages.chef.io/files/stable/chef-server/13.1.13/ubuntu/18.04/chef-server-core_13.1.13-1_amd64.deb

! 	Download NEW url
!! https://downloads.chef.io/products/infra-client?os=ubuntu	-- 14.0.65

wget https://packages.chef.io/files/stable/chef-server/14.0.65/ubuntu/18.04/chef-server-core_14.0.65-1_amd64.deb

sudo dpkg -i chef-server-core_*.deb

sudo chef-server-ctl reconfigure


### Create administrator account


sudo chef-server-ctl user-create USER_NAME FIRST_NAME LAST_NAME EMAIL 'PASSWORD' --filename FILE_NAME

sudo chef-server-ctl user-create ilkovi Ivan Iklov email ilkov_i@abv.bg 'Password1' --filename ~/.chef/ilkovi.pem

sudo chef-server-ctl user-create admin Ivan Iklov email admin@localhost 'Password1' --filename ~/.chef/admin.pem


----verify

sudo chef-server-ctl user-list


### Create an organization

sudo chef-server-ctl org-create it_power 'Company IT Power, Inc.' \
--association_user ilkovi \
--filename ~/.chef/mycompany-validator.pem


sudo chef-server-ctl org-create it_power 'Company IT Power, Inc.' \
--association_user admin \
--filename ~/.chef/chef-validator.pem


----verify

sudo chef-server-ctl org-list

### Install Chef Manage

sudo chef-server-ctl install chef-manage 
sudo chef-server-ctl reconfigure 
sudo chef-manage-ctl reconfigure


!! FIX : sudo chef-manage-ctl reconfigure --accept-license

### IF using UFW firewall

sudo ufw allow proto tcp from any to any port 80,443


### You should be able to access the Chef web admin dashboard on

https://192.168.0.103/login










##### ON Workstation

### Install Chef DK from .deb package

https://computingforgeeks.com/how-to-install-chef-development-kit-workstation-on-ubuntu/

VER=4.9.7

wget https://packages.chef.io/files/stable/chefdk/${VER}/ubuntu/18.04/chefdk_${VER}-1_amd64.deb

sudo apt install ./chefdk_${VER}-1_amd64.deb


!! To use the Chef development kit version of Ruby as the default Ruby,
!! you’ll need to edit your $PATH and GEM environment variables to include paths to the Chef development kit.

echo 'eval "$(chef shell-init bash)"' >> ~/.bash_profile


*Installation of Chef Development Kit will provide the following command line tools:
- chef
- chef-apply
- chef-client
- chef-shell
- chef-solo
- chef-vault







# Install Chef Workstation from .deb package


VER="20.12.205"

wget https://packages.chef.io/files/stable/chef-workstation/${VER}/ubuntu/18.04/chef-workstation_${VER}-1_amd64.deb

sudo apt install ./chef-workstation_${VER}-1_amd64.deb


* by installing Chef Workstation, you’ll get knife command.







### How to configure Chef Knife, Upload Cookbooks and Run a recipe on Chef Client Nodes
https://computingforgeeks.com/how-to-configure-chef-knife-upload-cookbooks-and-run-a-recipe-on-client-nodes/


 Knife is a command-line tool that provides an interface between your workstation and the Chef server. 
 The  knife enables you to upload your cookbooks to the Chef server and interact with nodes,
 the servers that you manage.

In summary, knife enables you to manage:

- Nodes – Servers managed by Chef
- Cookbooks and recipes
- Roles, Environments, and Data Bags
- Resources within various cloud environments
- The installation of the chef-client onto nodes
- Searching for indexed data on the Chef server

*knife requires two files to authenticate with the Chef server.
1. An RSA private key:  
Every request to the Chef server is authenticated through an RSA public key pair
The Chef server holds the public part; you hold the private part.

2. A knife configuration file
- he configuration file is typically named knife.rb.
- This configuration file contains information such as the Chef server’s URL, 
  the location of your RSA private key, and the default location of your cookbooks.
- Both of these files are typically located in a directory named .chef
- By default, every time knife runs, it looks in the current working directory for the .chef directory
- If the .chef directory does not exist, knife searches up the directory tree for a .chef directory


# Configure GIT


chef generate repo chef-repo	# in order to generate the skeleton
cd chef-repo
mkdir .chef
echo ".chef" > .gitignore
git config --global user.name Developer-4599
git config --global user.email newbe@abv.bg


git add .
git commit -m "initial commit"


==git clone git@github.com:Developer-4599/chef.git


# knife configure --initial

cd .chef

ssh-keygen -b 4096 -t rsa -C chef-workstation
ssh-copy-id master

scp master:/home/ilkovi/.chef/admin.pem .
scp master:/home/ilkovi/.chef/mycompany-validator.pem .


vim knife.rb

current_dir = File.dirname(__FILE__)
log_level               :info
log_location            STDOUT
node_name               "admin"
client_key              "/home/ilkovi/chef-repo/.chef/admin.pem"
validation_client_name  'mycompany'
validation_key          "/home/ilkovi/chef-repo/.chef/mycompany-validator.pem"
chef_server_url         "https://master/organizations/mycompany"
cache_type              'BasicFile'
cache_options( :path => "#{ENV['HOME']}/.chef/checksums" )
cookbook_path ["#{current_dir}/../cookbooks"]


# Fetch the SSL certificate from your Chef server.

knife ssl fetch

# Validate the downloaded SSL certificate

knife ssl check

file trusted_certs/master.crt


# Confirm that knife.rb is set up correctly by running the client list

knife client list
