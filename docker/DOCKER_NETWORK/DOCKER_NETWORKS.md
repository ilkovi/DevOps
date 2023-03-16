



#### List Docker Networks

# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
5c8fed284b38        bridge              bridge              local
e79d20c55b23        host                host                local
308d3aca06cd        none                null                local


docker network inspect bridge        - "Return json format information about the network details"


# Get the IP range of the bridge network

[root@linux-host ~]# docker network inspect bridge | jq '.[] .IPAM .Config[] .Subnet'
"172.17.0.0/16"



### Bridge Networking Deep Dive
The bridge network represents the docker0 network present in all Docker installations. Unless you specify otherwise
with the docker run --network=<NETWORK> option, the Docker daemon connects containers to this network by default.


It has 4 main components

• Docker0 Bridge
• Network Namespace
• Veth Pair
• External Communication


#### Files for Network Namespaces on Linux

ls -la /var/run/netns

But from the command ip netns list, we can’t get the container’s network namespace. The reason is because
docker deleted all containers network namespaces information from /var/run/netns.
We can get all docker container network namespace from /var/run/docker/netns.




#### Files for Docker Network Namespaces

/var/run/docker/netns




### How to get the detail information (like veth) about the container network namespace?


#### First we should get the pid of this container process, and get all namespaces about this container.

# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
eef7d1d1c6a2        centos:7            "/bin/bash -c 'while "   13 minutes ago      Up 13 minutes                           test1

# docker inspect --format '{{.State.Pid}}' eef7d1d1c6a2
19067

# ls -l /proc/19067/ns/
total 0
lrwxrwxrwx 1 root root 0 Oct  4 10:06 ipc -> ipc:[4026532612]
lrwxrwxrwx 1 root root 0 Oct  4 09:53 mnt -> mnt:[4026532610]
lrwxrwxrwx 1 root root 0 Oct  4 09:53 net -> net:[4026532615]
lrwxrwxrwx 1 root root 0 Oct  4 10:06 pid -> pid:[4026532613]
lrwxrwxrwx 1 root root 0 Oct  4 10:06 user -> user:[4026531837]
lrwxrwxrwx 1 root root 0 Oct  4 10:06 uts -> uts:[4026532611]

# ln -s /proc/19067/ns/net /var/run/netns/19067

# ip netns list
19067 (id: 1)
demo
test1 (id: 0)


### list the veth pair

ip netns exec 19067 ip link

12: eth0@if13: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0


ip link

13: veth2035f2a@if12: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP mode DEFAULT
    link/ether 56:3b:06:2e:bb:be brd ff:ff:ff:ff:ff:ff link-netnsid 1



"The bridge docker0 is up, and there is a veth pair created, one is in localhost, and another is in container’s network namspace."


After all is done, please remove /var/run/netns/3090