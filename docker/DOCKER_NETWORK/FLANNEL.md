
### PLEASE NOTE

We use "etcd" as management plane and docker build-in "overlay"
network as data plane to show how containers in different host connect with each other.

This time we will use flannel to do almost the same thing.

Flannel is created by CoreOS and it is a network fabric for containers, designed for Kubernetes.


* Flannel runs as a agent
* Flanneld on each host and is responsible for allocating a subnet lease out of a preconfigured address space
* Flannel uses "etcd" to sotre the network configuration, allocated subnets and auxiliary data
* packets forwarding is achieved using several strategies that are known as backends
* UDP packets over TUN device to


### Ilustration

Container1 veth ( 10.1.15.2/24 ) --> docker0 (10.1.15.1/24) -->
flannel0 (10.1.15.0/16 - flanneld) --> host eth 192.168.0.100 -->

packet Outer IP source 192.168.0.100 and dest 192.168.0.200
packet Inner IP source 10.1.15.2 and dest 10.1.20.3

host2 eth 192.168.0.200 --> flanneld - flannel0 10.1.20.0/16 -->
docker0 10.1.20.1/24 --> 10.1.20.3/24 veth0 - container2



### Info

https://github.com/coreos/flannel




### Installation

# When ETCD is installed and configured

 etcdctl cluster-health

member 2a67c16dd12021cc is healthy: got healthy result from http://10.100.26.57:2379
member c537f586a9823902 is healthy: got healthy result from http://10.100.26.9:2379



## Download flannel both on node1 and node2

cd /usr/bin

wget https://github.com/coreos/flannel/releases/download/v0.6.2/flanneld-amd64 -O flanneld && chmod 755 flanneld



## Config file

flannel will read the configuration from etcd   

{
"Network": "10.0.0.0/8",
"SubnetLen": 20,
"SubnetMin": "10.10.0.0",
"SubnetMax": "10.99.0.0",
"Backend": {
"Type": "vxlan",
"VNI": 100,
"Port": 8472
}
}

## Modify ETCD


node2 # ./etcdctl set /coreos.com/network/config '{"Network": "10.0.0.0/8", "SubnetLen": 20, "SubnetMin": "10.10.0.0", "SubnetMax": "10.99.0.0",  "Backend": {"Type": "vxlan","VNI": 100,"Port": 8472}}' {"Network": "10.0.0.0/8", "SubnetLen": 20,  "SubnetMin": "10.10.0.0","SubnetMax": "10.99.0.0",  "Backend": {"Type": "vxlan","VNI": 100,"Port": 8472}}


## Check
node2 # etcdctl get /coreos.com/network/config
{"Network": "10.0.0.0/8", \
 "SubnetLen": 20, "SubnetMin": "10.10.0.0", "SubnetMax": "10.99.0.0", \
 "Backend": {"Type": "vxlan","VNI": 100,"Port": 8472}}

## Start flannel on host1:

 nohup ./flanneld -iface=10.100.26.9 &
 
 