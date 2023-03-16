


# brctl show docker0
bridge name     bridge id               STP enabled     interfaces
docker0         8000.0242553f64d0       no              veth2035f2a

# ip link  | grep veth2035f2a
13: veth2035f2a@if12: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP mode DEFAUL


### Run second container

# docker run -d --name test2 centos:7 /bin/bash -c "while true; do sleep 3600; done"

# brctl show docker0
bridge name     bridge id               STP enabled     interfaces
docker0         8000.0242553f64d0       no              veth2035f2a
                                                        veth3b8a2cd


#  ip link | grep veth3b8a2cd
15: veth3b8a2cd@if14: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP mode DEFAULT





### GET CONTAINER IP Address

$ docker inspect --format '{{.NetworkSettings.IPAddress}}' test1
172.17.0.2
$ docker inspect --format '{{.NetworkSettings.IPAddress}}' test2
172.17.0.3


# docker exec test1 bash -c 'ping 172.17.0.3'
PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.
64 bytes from 172.17.0.3: icmp_seq=1 ttl=64 time=0.088 ms
64 bytes from 172.17.0.3: icmp_seq=2 ttl=64 time=0.048 ms



### Understand Docker network

Libnetwork implements Container Network Model (CNM) which formalizes the steps required to provide networking
for containers while providing an abstraction that can be used to support multiple network drivers.
During the Network and Endpoints lifecycle, the CNM model controls the IP address assignment for network and
endpoint interfaces via the IPAM driver(s) 

When creating the bridge docker0, libnetwork will do some request to IPAMdriver, something like network gateway,
address pool. When creating a container, in the network sandbox, and endpoint was created, libnetwork will request
an IPv4 address from the IPv4 pool and assign it to the endpoint interface IPv4 address.




### Containers Access internet trough host iptables

# iptables -L -t nat
Chain POSTROUTING (policy ACCEPT)
target     prot opt source               destination
MASQUERADE  all  --  172.17.0.0/16        anywhere    


