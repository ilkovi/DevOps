
https://www.linode.com/docs/guides/install-a-chef-server-workstation-on-ubuntu-18-04/

### Bootstrap a Node

Bootstrapping a node installs the Chef client on the node and validates the node. 
This allows the node to read from the Chef server and pull down and apply any 
needed configuration updates detected by the chef-client.


# Update the /etc/hosts file on the node to identify the node, Chef server’s domain name, and the workstation.
# From your workstation, navigate to your ~/chef-repo/.chef directory:

vi /etc/hosts

....ip node....

cd ~/chef-repo/.chef

- As the node’s root user
knife bootstrap 192.0.2.0 -x root -P password --node-name nodename

- As a user with sudo privileges
knife bootstrap 192.0.2.0 -x username -P password --use-sudo-password --node-name nodename

- As a user with key-pair authentication
knife bootstrap 192.0.2.0 --ssh-user username --sudo --ssh-identity-file ~/.ssh/id_rsa.pub --node-name hostname 

knife bootstrap 192.168.0.105 -U ilkovi --sudo --ssh-identity-file ~/.ssh/id_rsa.pub --node-name node

knife bootstrap 192.168.0.105 -U ilkovi -N node --sudo --use-sudo-password


# Check the client
 knife client show node
 
 