

####    Assign IP address to veth interface

ip addr add 192.168.1.1/24 dev veth-a

ip link set veth-a up

"Please note that the veth-a device will show DOWN state"



####    Assing IP address to the second veth-b interface

ip netns exec test1 ip addr add 192.168.1.2/24 dev veth-b

ip netns exec test1 ip link set dev veth-b up



#### If we check now, the intefaces are up

ip link


ip netns exec test1 ip link

ping 192.168.1.2