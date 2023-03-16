



### Create two containers 

[root@linux-host ~]# docker run -d --name test1 busybox sh -c "while true;do sleep 3600;done"
dc9c729e29cb6b7d85d2c13d6a54c1716672e77384001f11caa806df361b162d
[root@linux-host ~]# docker run -d --name test2 busybox sh -c "while true;do > sleep 3600;done"
cf05160e0ad09b0a761b1ab0f07ba03ad80597eea7f55b30069ec5867bd20c13


### Create new bridge

[root@linux-host ~]# docker network create -d bridge demo-bridge
445b4008c01a228dd93b5f59c7304f6e2f66006966bf0b32097894e38ffde49b


### Show bridge
[root@linux-host ~]# brctl show
bridge name     bridge id               STP enabled     interfaces
br-b521917dadbc         8000.024234254e06       no


#### Move Container to the Bridge
PLEASW NOTE:
"This command add second veth to Container test1 "

docker network connect demo-bridge test1


# brctl show br-b521917dadbc
bridge name     bridge id               STP enabled     interfaces
br-b521917dadbc         8000.024234254e06       no              veth9b9d405



### Add the second container to demo-bridge
docker network connect demo-bridge test2

PLEASE NOTE:
"now containers become pingabel the thier container names"

test1# ping test2
PING test2 (172.19.0.3): 56 data bytes
64 bytes from 172.19.0.3: seq=0 ttl=64 time=0.098 ms




#### Disconnect Conatiner from Network bridge
docker network disconnect demo-bridge test1



