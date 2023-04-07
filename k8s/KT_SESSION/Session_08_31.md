
### docker / practile tasks from exam
- git repo

https://github.com/alfahami/hands-on-docker


### Certification 
in Linux Fundation
CKA   - ceritified Kubernetes Administator	$395
SKAD  - certified Kubernetes Developer		$395

$ 395 - you could go twice ( if you not pass from the first time )
https://training.linuxfoundation.org/full-catalog/?_sft_product_type=certification



Duration of Exam 2 hours
Questions/practicle tasks - 25
Certification Valid for 3 Years 








### Install documentation

https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository







### K9S		( K8s GUI )

K9S --> https://k9scli.io/   some gui for kubernetes

https://github.com/derailed/k9s/releases  - 
download the deb package or tar
chmod +x ...
move to /usr/bin

!! help in the top

!! shift + : ( like vim ) 
you could execute commands

!! CTR + a 
list all available commands

!! CTR + c
exit


When looking in K9s
mark container , s -> shell directly on the container
remart -> to exit






### GCP

docker ( runtime )

now

dockerd ( runtime is now )








### Install kubernetes on Ubuntu

Prereq:
Install Docker
sudo usermod -aG docker $USER



!! install on linux
wget https://github.com/kubernetes/minikube/releases/download/v1.25.2/minikube_1.25.2-0_amd64.deb
dpkg -i minikube_1.25.2-0_amd64.deb

!! isntall on WSL
sysctl -w net.bridge.bridge-nf-call-iptables=1

swapoff -a

minikube start --driver=docker

apt-get update

mkdir -p /etc/apt/keyrings

echo "deb [arch$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

apt-det install docker-ce docker-ce-cli containerd.io docker-compose-plugin

mkdir /etc/docker

vi /etc/docker/daemon.json
	"iptables" : false
	}

service start docker

service docker start

docker ps

minikube start --driver=docker

!! common Steps
minikube config set WantUpdateNotification false
minikube start --driver=docker --force
minikube status
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod 777 kubectlcp kubectl /usr/bin
kubectl
echo "source <(kubectl completion bash)" >> ~/.bashrc
exit



# TEST
** minikube do not downlaod from github ....
( on WSL )


kubectl run nginx --image=nginx --restart=Never


# check

how to start ingress on minikube 
- it is minikube command

or you could run busybox container and access nginx 

ingress -> points to -> serivce
- you need service to use the ingress once configured




!! configure vi to use collumns so it is conviniet to edit YAML


# HANDS-on
https://github.com/dgkanatsios/CKAD-exercises




