

#### Bridge0 come with Docker installation and you could see it in the main network stack


ip link


Use the following command: "brctl - ethernet bridge administration"


brctl show 

NOTE "If you can’t find brctl command, you can install it.
 For CentOS, please use sudo yum install bridge-utils.
 For Ubuntu, please use apt-get install bridge-utils" 


brctl show
bridge name     bridge id               STP enabled     interfaces
docker0         8000.0242553f64d0       no
virbr0          8000.52540059db8d       yes             virbr0-nic
virbr1          8000.525400a9585c       yes             virbr1-nic


$ docker run -d --name test1 centos:7 /bin/bash -c "while true; do sleep 3600; done"
$ docker ps


#### Now you could see that docker0 bridge is up

The bridge docker0 is up, and there is a veth pair created, one is in localhost, and another is in container’s network namspace.

ip li

