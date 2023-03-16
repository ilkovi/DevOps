1. Installation

- single node
http://www.greenreedtech.com/installing-red-hat-openshift/

- multiple nodes
https://blog.zencoffee.org/2016/07/deploying-openshift-origin-centos-atomic/

1. Download from github the openshift-ansible  project

yum install -y epel-release
yum install ansible python-cryptography python-crypto pyOpenSSL git
git clone https://github.com/openshift/openshift-ansible


2. Configure default /etc/ansible/hosts  file
[OSEv3:children]
masters
nodes
etcd
lb

[OSEv3:vars]
ansible_ssh_user=root
deployment_type=origin
openshift_release=1.3.1
openshift_master_identity_providers=[{'name': 'htpasswd_auth', 'login': 'true', 'challenge': 'true', 'kind': 'HTPasswdPasswordIdentityProvider', 'filename': '/etc/origin/master/htpasswd'}]
openshift_master_htpasswd_users={'admin': '$apr1$Nc6yINJX$H7TqmPu14U8OTwtosFTEe1'}
openshift_docker_disable_push_dockerhub=True
openshift_master_default_subdomain=os.localdomain
osm_default_node_selector='region=primary'
osn_storage_plugin_deps=['nfs']

openshift_hosted_router_selector='region=infra'
openshift_hosted_registry_selector='region=infra'

openshift_override_hostname_check=false

[masters]
os-master1

[etcd]
os-brlb1
os-brlb2

[lb]
os-brlb1
os-brlb2

[nodes]
os-master1 openshift_node_labels="{'region': 'infra', 'zone': 'default'}" openshift_schedulable=True
os-node1 openshift_node_labels="{'region': 'primary', 'zone': 'left'}"
os-node2 openshift_node_labels="{'region': 'primary', 'zone': 'right'}"



3. Run the play book
ansible-playbook ~/openshift-ansible/playbooks/byo/config.yml





 Commands

### Log in
oc login -u system:admin -n default

### Get token
oc config view --flatten

### List PODs
oc get pods

### Others

oc status
oc project
