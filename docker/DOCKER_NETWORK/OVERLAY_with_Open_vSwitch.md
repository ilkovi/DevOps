

There are at least two ways connect containers with open vSwitch.
• connect default docker0 with ovs bridge
• connect container with ovs bridge directly through veth pair.


We will chose the first way, becuase it’s easier.
For the second way, if don’t use the default docker0 bridge, we will
need to do more work toconnect containers with ovs, such as create
network namespace and veth pair manully, attach
veth to container, resolve ip address management, NAT, etc.


### Connection

container1 eth0(veth) --> Linux bridge docker0 -->
"Open vSwitch" br-int --> gre0 -->
"GRE tunnel" 
"Open vSwitch" gre0 --> br-int --> 
Linux bridge docker0 --> eth0 (veth) conainer2 



### TEST

node2 # docker run -d --name container1 centos:7 /bin/bash -c "while true; do sleep 3600; done"

node1 # docker run -d --name container1 centos:7 /bin/bash -c "while true; do sleep 3600; done"

node1 # docker run -d --name container2 centos:7 /bin/bash -c "while true; do sleep 3600; done"


node2 # docker inspect --format '{{.NetworkSettings.IPAddress}}' container1
172.17.0.2


node1 # docker inspect --format '{{.NetworkSettings.IPAddress}}' container1
172.17.0.2

node1 # docker inspect --format '{{.NetworkSettings.IPAddress}}' container2
172.17.0.3

#### Stop the container on node1 because it have the same IP address

node1 # docker stop container1
container1


node1 # docker exec -it container2 bash
[root@bd36f52e3aa1 /]# ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
From 172.17.0.3 icmp_seq=1 Destination Host Unreachable







### Install OVS

node1 # yum install -y openvswitch.x86_64
node1 # systemctl start openvswitch.service
node1 # chkconfig openvswitch on


## Create a ovs bridge and a veth pair

 # ovs-vsctl add-br br-int
 
 # ovs-vsctl show
2491c9a2-8ffe-46b9-b7b5-013419ac4c74
    Bridge br-int
        Port br-int
            Interface br-int
                type: internal
    ovs_version: "2.0.0"

 # ip link add veth0 type veth peer name veth1




## Connect veth pair with dockre0 and ovs bridge br-int, set them up.

 ip link add veth0 type veth peer name veth1
 ovs-vsctl add-port br-int veth1
 brctl addif docker0 veth0
 ip link set veth1 up
 ip link set veth0 up
 ip link





## NODE2


node2 # yum install -y openvswitch.x86_64
node2 # systemctl start openvswitch.service
node2 # chkconfig openvswitch on

node2 # ovs-vsctl add-br br-int
node2 # ip link add veth0 type veth peer name veth1
node2 # ovs-vsctl add-port br-int veth1
node2 # brctl addif docker0 veth0
node2 # ip link set veth1 up
node2 # ip link set veth0 up




#### GER tunnel between host 1 and host 2


node1 # ovs-vsctl add-port br-int gre0 -- set interface gre0 type=gre options:remote_ip=10.100.26.57


node2 # ovs-vsctl add-port br-int gre0 -- set interface gre0 type=gre options:remote_ip=10.100.26.9

node2 # ovs-vsctl show
44caf757-c4bb-4f70-b4e8-4f9b99e4d632
    Bridge br-int
        Port br-int
            Interface br-int
                type: internal
        Port "veth1"
            Interface "veth1"
        Port "gre0"
            Interface "gre0"
                type: gre
                options: {remote_ip="10.100.26.9"}
    ovs_version: "2.0.0"

    
node2 # brctl show
bridge name     bridge id               STP enabled     interfaces
docker0         8000.024292ca835b       no              veth0
                                                        veth16d11d3
docker_gwbridge 8000.0242433d81f1       no              veth8aca59d
virbr0          8000.52540059db8d       yes             virbr0-nic
virbr1          8000.525400a9585c       yes             virbr1-nic


node2 # docker exec -it container1 bash
[root@b50700e85fa9 /]# ping 172.17.0.3
PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.
64 bytes from 172.17.0.3: icmp_seq=1 ttl=64 time=1.03 ms
64 bytes from 172.17.0.3: icmp_seq=2 ttl=64 time=0.220 ms



node1 # # tcpdump -i ens160 | grep 172.17
07:32:11.459843 IP 10.100.26.57 > linux-host: GREv0, length 102: IP 172.17.0.2 > 172.17.0.3: ICMP echo request, id 22, seq 1, length 64
07:32:11.460064 IP linux-host > 10.100.26.57: GREv0, length 102: IP 172.17.0.3 > 172.17.0.2: ICMP echo reply, id 22, seq 1, length 64




### Improvement
There are some improvements can be done for this lab:
• Create a new docket network instead of using the default docker0 bridge
• docker bridge on host 1 and host 1 have different network ip range for containers







