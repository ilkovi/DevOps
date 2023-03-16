


## Download RPM

- install RPM

rpm -ivh chef-server-core-14.11.36-1.sles12.x86_64.rpm

# reconfigure

chef-server-ctl reconfigure

chef-server-ctl status




# Generate admin and validator certificates


mkdir ~/.chef


` Run the following command to create an administrator
chef-server-ctl user-create admin Ivan Iklov email ilkov_i@abv.bg 'Password1' --filename ~/.chef/admin.pem


` Run the following command to create an organization
chef-server-ctl org-create it_power 'Company IT Power, Inc.' --association_user admin --filename ~/.chef/chef-validator.pem




# Install Chef Manage - deprecated !! --- NOW CHEF AUTOMATE 

chef-server-ctl install chef-manage 

chef-server-ctl reconfigure 

` or download deprecated RedHat rpm from https://www.chef.io

chef-manage-3.2.20-1.el7.x86_64.rpm

rpm -ivh chef-manage-3.2.20-1.el7.x86_64.rpm
`warning: chef-manage-3.2.20-1.el7.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 83ef826a: NOKEY
`Preparing...                          ################################# [100%]
`Updating / installing...
`   1:chef-manage-3.2.20-1.el7         ################################# [100%]
`
`Thank you for installing the Chef Management Console add-on!
`
` The next step in the process is to run:

chef-manage-ctl reconfigure


` allow 80 , 443 in the FW

` log on https:IP

` use the user admin ( configured above and the respective password )










## Configure KNIFE

ln -s /opt/opscode/bin/knife /usr/bin/knife

vi /root/.chef/knife.rb

current_dir = File.dirname(__FILE__)
log_level               :info
log_location            STDOUT
node_name               "admin"
client_key              "#{current_dir}/admin.pem"
validation_key          "#{current_dir}/chef-validator.pem"
chef_server_url         "https://chef:443/organizations/it_power"
syntax_check_cache_path "#{ENV['HOME']}/.chef/syntax_check_cache"
cookbook_path           ["#{current_dir}/../cookbooks"]
ssl_verify_mode         :verify_none
cookbook_copyright      "IT POWER, Inc."
knife[:editor]="vim"
knife[:ssh_user]="root"




chef:~/.chef # knife ssl fetch
`WARNING: Certificates from chef will be fetched and placed in your trusted_cert
`       directory (/root/.chef/trusted_certs).
`
`       Knife has no means to verify these are the correct certificates. You should
`       verify the authenticity of these certificates after downloading.
`Adding certificate for chef in /root/.chef/trusted_certs/chef.crt



chef:~/.chef # knife ssl check
`Connecting to host chef:443
`Successfully verified certificates from `chef'


# check

chef:~/.chef # knife config show
Loading from credentials file /root/.chef/credentials
Loading from configuration file /root/.chef/knife.rb
chef_server_url:         https://chef:443/organizations/it_power
client_key:              /root/.chef/admin.pem
config_file:             /root/.chef/knife.rb
cookbook_copyright:      IT POWER, Inc.
cookbook_path:           /root/cookbooks
knife:
  editor:   vim
  ssh_user: root
log_level:               warn
log_location:            STDOUT
node_name:               admin
profile:                 default
ssl_verify_mode:         verify_none
syntax_check_cache_path: /root/.chef/syntax_check_cache
validation_key:          /root/.chef/chef-validator.pem




