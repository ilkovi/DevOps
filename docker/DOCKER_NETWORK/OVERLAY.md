The Docker Overlay driver has existed since Docker Engine 1.9,
and an external K/V store was required to manage state for the network. 

Docker Engine 1.12 integrated the control plane state into Docker Engine so that an
external store is no longer required.

During overlay network creation, Docker Engine creates the network infrastructure required for overlays on each host
(Create on one host, and through etcd sync to the other host). A Linux bridge is created per overlay along with its
associated VXLAN interfaces. The Docker Engine intelligently instantiates overlay networks on hosts only when a
container attached to that network is scheduled on the host. This prevents sprawl of overlay networks where connected
containers do not exist.

There are two interfaces in each container,
 one is for docker_gwbridge network,
 and the other is for demo overlay network.

# Connection between two container in overlay NW

container1 -> eth0 10.0.0.2 (veth) -> Docker_gwbridge -> eth0 (node1) -->
physical network eth0(node2) -> Dcker_gwbridge -> eth0 (veth) container2

container1 -> eth1(veth) -> demo(overlay nw) <--> eth1(veth) container2




# MGMT pane
We chose etcd 2 as our external key-value store. You can trade etcd cluster as 
the management plane in this multi-host networking.

# Data pane
For data plane, The Docker overlay network encapsulates container traffic in a VXLAN header which allows the traffic
to traverse the physical Layer 2 or Layer 3 network.