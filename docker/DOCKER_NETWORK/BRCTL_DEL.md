




# brctl show
bridge name     bridge id               STP enabled     interfaces
dev             8000.000000000000       no
docker0         8000.0242b963686b       no
virbr0          8000.52540059db8d       yes             virbr0-nic
virbr1          8000.525400a9585c       yes             virbr1-nic


# brctl delif dev virbr1-nic
device virbr1-nic is not a slave of dev


# brctl delif virbr1 virbr1-ni

# brctl delbr virbr1
bridge virbr1 is still up; can't delete it'

ifconfig virbr1 down
brctl delbr virbr1


brctl delif virbr0 virbr0-nic
ifconfig virbr0 down
brctl delbr virbr0






OR


# ip link delete virbr1-nic
# ip link delete virbr0-nic


# ip link delete virbr1
# ip link delete virbr0

