

# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
5c8fed284b38        bridge              bridge              local
e79d20c55b23        host                host                local
308d3aca06cd        none                null                local

# docker network create -d bridge demo
0d459dd2cc2d750e52decf33fd3cbb459fe6a0c86937973c70cdb57912057f70

# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
5c8fed284b38        bridge              bridge              local
0d459dd2cc2d        demo                bridge              local
e79d20c55b23        host                host                local
308d3aca06cd        none                null                local



# docker network rm demo
demo

# docker network create -d bridge my-bridge
09e66ac13b56329849a1bc990aafd757b54bed612bdb706a58ca656fa89a55b3

# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
5c8fed284b38        bridge              bridge              local
e79d20c55b23        host                host                local
09e66ac13b56        my-bridge           bridge              local
308d3aca06cd        none                null                local


# ip a
25: br-09e66ac13b56: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN
    link/ether 02:42:95:c1:e3:49 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 scope global br-09e66ac13b56
       valid_lft forever preferred_lft forever
       
 
# brctl show br-09e66ac13b56
bridge name     bridge id               STP enabled     interfaces
br-09e66ac13b56         8000.024295c1e349       no



# Create a container connected to my-bridge
docker run -d --name test1 --network my-bridge busybox sh -c "while true;do sleep 3600;done"

# docker exec -it test1 sh

# ip a
26: eth0@if27: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue
    link/ether 02:42:ac:12:00:02 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.2/16 scope global eth0

#### Outside the container
ip a
25: br-09e66ac13b56: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP
    link/ether 02:42:95:c1:e3:49 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 scope global br-09e66ac13b56
       valid_lft forever preferred_lft forever
    inet6 fe80::42:95ff:fec1:e349/64 scope link
       valid_lft forever preferred_lft forever
       
27: vetha383357@if26: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-09e66ac13b56 state UP
    link/ether b2:d9:7b:a4:49:b4 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::b0d9:7bff:fea4:49b4/64 scope link
       valid_lft forever preferred_lft forever


# brctl show
bridge name     bridge id               STP enabled     interfaces
br-09e66ac13b56         8000.024295c1e349       no              vetha383357






### DELETE BRIDGE

# brctl show
bridge name     bridge id               STP enabled     interfaces
virbr0          8000.52540059db8d       yes             virbr0-nic

# ifconfig virbr0 down
# brctl delbr virbr0









