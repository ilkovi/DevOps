

### Kubernetes components

Kubernetes has 3 main containers in the kube-system namespace
* kube-proxy 	= The Kubernetes proxy is responsible for routing network traffic to load-balanced
		   services in the Kubernetes cluster

* kube-dns	= provides naming and discovery for the services that are defined in the cluster.

* kubernetes-dashboard 		=  kubernetes UI ( run kubectl proxy , to run locally on port 8001 )




Kubernetes has the foolowing components

* Kubernetes uses namespaces to organize objects in the cluster
It is like different folders / or resource pools in vCenter


* Contexts If you want to change the default namespace more permanently, you can use a
context. This gets recorded in a kubectl configuration file, usually located at
$HOME/.kube/config

 - create a context with different default namespace
$ kubectl config set-context my-context --namespace=mystuff

 - start using it
$ kubectl config use-context my-context

Contexts can also be used to manage different clusters or different users for
authenticating to those clusters using the --users or --clusters flags with the
set-context command


It is like defferent clusters in the same vCenter




* API Objects

Everything contained in Kubernetes is represented by a RESTful resource.
Throughout this book, we refer to these resources as Kubernetes objects. Each
Kubernetes object exists at a unique HTTP path; for example, https://yourk8s.
com/api/v1/namespaces/default/pods/my-pod leads to the representation of a
pod in the default namespace named my-pod.










### CNI - container network interface plugin

# calico / flannel 

A CNI plug-in is responsible for allocating network interfaces to the newly created containers. Kubernetes first creates a container without a network interface and then calls a CNI plug-in. The plug-in configures container networking and returns information about allocated network interfaces, IP addresses, etc. The parameters that Kubernetes sends to a CNI plugin, as well as the structure of the response must satisfy the CNI specification, but the plug-in itself may do whatever it needs to do its job.





### Create bridge and assign IP address to it

$ sudo brctl addbr cni0
$ sudo ip link set cni0 up
$ sudo ip addr add <bridge-ip>/24 dev cni0

# view the rougitng created for this bridge device

$ ip route | grep cni0
10.244.0.0/24 dev cni0  proto kernel  scope link  src 10.244.0.1


How CNI works
1. find free ip addresses with nmap
2. Create namespace
3. create bridge veth devices in that namespace
4. let the requestore KUBELET knows about it
5. allow containter to container network in IPTABLES
  - container to host have ping, because INPUT chain as local address is applied 
  - container to container doesn't have ping because FORWARD chaning Docker rule Drop ( for sec reasons )

$ sudo iptables -t filter -A FORWARD -s 10.244.0.0/16 -j ACCEPT
$ sudo iptables -t filter -A FORWARD -d 10.244.0.0/16 -j ACCEPT

6. NAT, so container could access ouside world

$ sudo iptables -t nat -A POSTROUTING -s 10.244.0.0/24 ! -o cni0 -j MASQUERADE

7. Route for containers on same subnet on different hosts

$ gcloud compute routes create k8s-master --destination-range 10.244.0.0/24 --network k8s --next-hop-address 10.128.0.2




regarding 2
    mkdir -p /var/run/netns/
    ln -sfT $CNI_NETNS /var/run/netns/$CNI_CONTAINERID

The CNI spec tells the caller (in our case, kubelet) to create a network namespace and pass it in the CNI_NETNS environment variable. Here, we are creating a symlink that points to the network namespace and is located in the /var/run/netns/ folder (this is something that the ip netns tool assumes). After those commands are executed, we will be able to access the CNI_NETNS namespace using the ip netns $CNI_CONTAINERID command.

















