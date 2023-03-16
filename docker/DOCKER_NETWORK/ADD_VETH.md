


#### Add virtual PAIR interfaces in a LINUX Name Space

ip link add veth-a type veth peer name veth-b

ip link


# You could see that theese virtual interfaces are under default name space


#### Move one of the adapters in the test1 name space

ip link set veth-b netns test1


# check

 ip link
 
 ip link set veth-b netns test1


