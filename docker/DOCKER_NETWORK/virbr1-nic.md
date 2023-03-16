


# ifconfig -a

virbr0-nic: flags=4098<BROADCAST,MULTICAST>  mtu 1500

virbr1-nic: flags=4098<BROADCAST,MULTICAST>  mtu 1500

# virsh net-list
 Name                 State      Autostart     Persistent
----------------------------------------------------------
 default              active     yes           yes
 docker-machines      active     yes           yes

# virsh net-destroy docker-machines
Network docker-machines destroyed

# virsh net-destroy default
Network default destroyed








####### Removing virbr0 interface on running machines ( non-persistence across reboots )

1. First, list out the virtual bridge interfaces available on the system using the below command.

# brctl show
bridge name     bridge id               STP enabled     interfaces
virbr0          8000.5254003008b6       yes             virbr0-nic

2. Make the bridge interface down before removal.

# ip link set virbr0 down

3. Now, remove the bridge

# brctl delbr virbr0

4. check if the bridge is removed

# brctl show
bridge name     bridge id               STP enabled     interfaces




