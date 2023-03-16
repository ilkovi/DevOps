# for installation we use

https://blog.tekspace.io/setup-kubernetes-1-14-cluster-on-centos-7-6/

# for setup ports

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/



#### INSTALLATION

 useradd -m -d /home/kube -s /bin/bash -c "Kubernetes User" -u 1000 -U kube
 passwd kube
 usermod -aG wheel kube
 
 firewall-cmd --permanent --add-port=10250/tcp 
 firewall-cmd --permanent --add-port=30000-32767/tcp 
 firewall-cmd --reload
 
 firewall-cmd --list-all
 
 ==== on the master only
 firewall-cmd --permanent --add-port=6443/tcp
 firewall-cmd --permanent --add-port=2379-2380/tcp
 firewall-cmd --permanent --add-port=10250-10252/tcp
 ====
 
 
 
 yum install -y kubelet-1.15.3 kubeadm-1.15.3 kubectl-1.15.3 --disableexcludes=kubernetes

 systemctl enable kubelet && systemctl start kubelet
 
 
 # Pull images on the master
 kubeadm config images pull
 
 # Start INSTALLATION
 kubeadm init --pod-network-cidr=100.244.0.0/16


 # isntall flannel ( EDIT IT first to match the network above )
 
 kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/a70459be0084506e4ec919aa1c114638878db11b/Documentation/kube-flannel.yml
 
 


#### Output


kubeadm join 172.17.12.124:6443 --token ostgqy.yd51z2itikmm8fc6 \
    --discovery-token-ca-cert-hash sha256:ab8130c4f880ca6dc90d70758dd0dbd90b0f40af28dc2d45091e4ab237f9b517




#### Lable nodes on the master

 kubectl label node worker1 node-role.kubernetes.io/worker=worker
 kubectl label node worker2 node-role.kubernetes.io/worker=worker



#### NODES are NOT READY
https://gravitational.com/blog/kubernetes-flannel-dashboard-bomb/

# CoreDNS is not running
-----------------------
# fix

iptables -P FORWARD ACCEPT

systemctl stop kubelet
systemctl stop docker

iptables -F && iptables -t nat -F && iptables -t mangle -F && iptables -X

systemctl start kubelet
systemctl start docker

------------
- INSTALL FLANEL - bottom . Then CORE-DNS will be created





#### running the dashboard



 https://github.com/kubernetes/dashboard

 wget https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
 
 kubectl create -f kubernetes-dashboard.yaml



 # craete admin role and role binding
 
 https://medium.com/@kanrangsan/creating-admin-user-to-access-kubernetes-dashboard-723d6c9764e4
 
 
  kubectl apply -f dashboard-adminuser.yml
  kubectl apply -f admin-role-binding.yml



 
 # GENERATE CERTIFACTE for now not working with help ...
 
 cd /root/
 mkdir certs
 openssl req -nodes -newkey rsa:2048 -keyout certs/dashboard.key -out certs/dashboard.csr -subj "/C=/ST=/L=/O=/OU=/CN=kubernetes-dashboard"
 openssl x509 -req -sha256 -days 365 -in certs/dashboard.csr -signkey certs/dashboard.key -out certs/dashboard.crt
 kubectl create secret generic kubernetes-dashboard-certs --from-file=certs -n kube-system
 
 # Import dashboard.crt to your browser



 
 # get Token
 
 kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
 
 


