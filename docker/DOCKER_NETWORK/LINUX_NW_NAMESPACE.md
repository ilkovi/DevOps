

####     Linux Network Namespaces   

[root@linux-host ~]# ip netns list                  - "List Linux Network Namespace"

[root@linux-host ~]# ip netns add test1             - "Create Linux Network Namespace"

[root@linux-host ~]# ip netns list
test1

###      Check that location on the LINUX

ls -la /var/run/netns



[root@linux-host ~]# ip netns exec test1 ip a       - "List all networks in the test1 name space"

[root@linux-host ~]# ip netns del test1             - "Delete the test1 name space"



####     Execute CMD within Network Namespace

ip netns exec test1 ip a                            - "List all interfaces in the Linux Name Space test1"

[root@linux-host ~]# ip netns exec test1 ip a
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

# From the output we can see that the lo inteface is DOWN, we can run a command to let it up
    
[root@linux-host ~]# ip netns exec test1 ip link
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

# The status of lo became UNKNOWN, please ignore that    
    
[root@linux-host ~]# ip netns exec test1 ip link set dev lo up

[root@linux-host ~]# ip netns exec test1 ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
