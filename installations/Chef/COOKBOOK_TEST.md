
https://computingforgeeks.com/how-to-configure-chef-knife-upload-cookbooks-and-run-a-recipe-on-client-nodes/


### Write a test Chef Cookbook

# Generate cookbook

`SYNTAX: hef generate cookbook COOKBOOK_PATH/COOKBOOK_NAME (options)

cd ~/chef-repo
 
chef generate cookbook  cookbooks/install_apache


# Generate HTML index page template

cd cookbooks

chef generate template install_apache index.html

`Edit the file index.html.erb

vim install_apache/templates/index.html.erb

<html>
  <body>
    <h1>Hello Chef World from <%= node['fqdn'] %></h1>
  </body>
</html>

`The attribute node['fqdn'] a fully qualified domain name for a node



## Create a Recipe for Apache

- Install basic system packages – vim, bash-completion, curl & wget
- Install Apache web server package – httpd for CentOS/RHEL/Fedora and apache2 for Debian family
- Start Apache service and set it to start on boot
- Copy index.html.erb template to /var/www/html/index.html







nano install_apache/recipes/default.rb

# Install basic packages

package 'Install basic packages' do
  package_name %w(vim wget curl bash-completion)
end


# Install Apache web server
package 'Install Apache web server' do
  case node[:platform]
  when 'redhat', 'centos', 'fedora'
    package_name 'httpd'
  when 'ubuntu', 'debian'
    package_name 'apache2'
  end
end

# Start and enable the service

service 'Start and enable apache service' do
  case node[:platform]
  when 'redhat', 'centos', 'fedora'
    service_name 'httpd'
  when 'ubuntu', 'debian'
    service_name 'apache2'
  end
  action [:enable, :start]
end

# Copy apache template

template '/var/www/html/index.html' do
  source 'index.html.erb'
  mode '0644'
  case node[:platform]
  when 'redhat', 'centos', 'fedora', 'scientific'
    owner 'apache'
    group 'apache'
  when node[:platform]
    owner 'www-data'
    group 'www-data'
  end
end






` Also, edit your metadata file to specify cookbook version and Git repository URL for the same.

vim install_apache/metadata.rb





###  Upload Cookbook to Chef Server

knife cookbook upload install_apache

`Confirm by listing cookbooks on the Chef Server
knife cookbook list
*cron-delvalidate   0.1.3
*install_apache     0.1.0



`Add the recipe to your node’s run list
knife node run_list add node 'recipe[install_apache::default]'
*node:
*  run_list:
*    recipe[cron-delvalidate::default]
*    recipe[install_apache::default]


`Use knife-ssh to run the chef-client command on your node
knife ssh 'name:node' 'sudo chef-client' -x ilkovi


### Delete Node data

knife node delete nodename --yes
knife client delete nodename --yes

`knife node delete removes the node’s metadata from the Chef server and 
`knife client delete  deletes the entry (including the public part of the RSA key pair) 

# To delete your cookbook from the Chef server

knife cookbook delete cookbookname --all --yes
` If you omit the --all argument, you’ll be prompted to select which version to delete


# To delete the role from the Chef server

knife role delete myrole --yes



# Delete the RSA private key from your node

During the bootstrap process, an RSA private key is generated on your node to
enable your node to make API calls to the Chef server. This key is saved to
/etc/chef/client.pem on Linux systems
