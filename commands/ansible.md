### run ansible with a log

ansible-playbook -i inventory /usr/share/ansible/playbooks/openshift-glusterfs/config.yml -vv | tee -a ansible-$(date +%Y%m%d-%H%M).log

### Get list/man about ansible modules

ansible-doc -l 		--- list modules on local system

ansible-doc [..module_name..]   --- info and examples



### List inventory and vars

ansible-inventory --list -y


### Checks

ansible all -m ping -u ilkovi


### run regular commands

ansible all -a "df -h" -u ilkovi



### Install latest "vim"

ansible all -m apt -a "name=vim state=latest" -u ilkovi -b -K

-ask-become-pass, -K
 	ask for privilege escalation password; 
	
--become, -b 	run operations with become	
