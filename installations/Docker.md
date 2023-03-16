### 1. Install on RHEL

yum install -y yum-utils
yum-config-manager --enable rhel-7-server-extras-rpms
yum install docker



### 2. Firewall

firewall-cmd --zone=public --add-port=4243/tcp --permanent
firewall-cmd --reload


### 3. Configure additional disk

 pvcreate /dev/sdb

 vgcreate docker /dev/sdb

 lvcreate --wipesignatures y -n thinpool docker -l 95%VG

 lvcreate --wipesignatures y -n thinpoolmeta docker -l 1%VG

 lvconvert -y --zero n -c 512K --thinpool docker/thinpool --poolmetadata docker/thinpoolmeta
 
vi /etc/lvm/profile/docker-thinpool.profile
activation {
    thin_pool_autoextend_threshold=80
    thin_pool_autoextend_percent=20
}

 lvchange --metadataprofile docker-thinpool docker/thinpool
 
 lvs -o+seg_monitor


 
 
 
### 4. Setup PROXY

vi /etc/systemd/system/docker.service.d/http-proxy.conf
[Service]
Environment="HTTP_PROXY=http://PROXY_IP:3128/"
Environment="NO_PROXY=localhost,127.0.0.0/8"

# Flush changes and Restart Docker:

systemctl daemon-reload
systemctl restart docker

# Verify that the configuration has been loaded:

systemctl show --property Environment docker 




### 5. Setup certificates

 mkdir -p /var/docker
 cd /var/docker/
 openssl req -subj '/CN=localhost' -x509 -newkey rsa:4096 -nodes -keyout serverkey.pem -out server.pem -days 712


 
 

### 6. Configure STORAGE


" iT IS BETTER TO CONFIGURE DOCKER IN CUSTOM CONFS.
  BECAUSE IN UPDATE DOCKER.SERVICE GET OVERWRITTEN"


 cat /etc/docker/daemon.json
{
  "selinux-enabled": false,
  "hosts": ["tcp://0.0.0.0:2375","unix://var/run/docker.sock"],
  "tls": true,
  "tlscert": "/var/docker/server.pem",
  "tlskey": "/var/docker/serverkey.pem",
  "tlsverify": true,
  "storage-driver": "devicemapper",
  "storage-opts": [
    "dm.fs=xfs",
    "dm.thinpooldev=/dev/mapper/docker-thinpool",
    "dm.use_deferred_removal=true",
    "dm.use_deferred_deletion=true"
    ]
}





### 7. Start / Stop

systemctl enable docker.service
systemctl start docker


### 8. Logs

journalctl -u docker


### 9. Files

/var/lib/docker

Images are mounted under:

/var/lib/docker/devicemapper/devicemapper





### 10. FIXEX

#
DON'T BIND ON ANY IP ADDRESS WITHOUT setting -tlsverify IF YOU DON'T KNOW WHAT YOU'RE DOING [!]'

  "hosts": ["tcp://0.0.0.0:2375","unix://var/run/docker.sock"],
  "tls": true,
  "tlscert": "/var/docker/server.pem",
  "tlskey": "/var/docker/serverkey.pem",
  "tlsverify": true,

  
#
level=warning msg="could not change group /var/run/docker.sock to docker: group docker not found"


groupadd docker
usermod -aG docker $USER
  
# slow start

"Remove the following from /usr/lib/systemd/system/docker.service   "

> Description=Docker Application Container Engine
> Documentation=http://docs.docker.com
5a11,14
> EnvironmentFile=-/run/containers/registries.conf
> EnvironmentFile=-/etc/sysconfig/docker
> EnvironmentFile=-/etc/sysconfig/docker-storage
> EnvironmentFile=-/etc/sysconfig/docker-network
16c25,31
<           --seccomp-profile=/etc/docker/seccomp.json
---
>           --seccomp-profile=/etc/docker/seccomp.json \
>           $OPTIONS \
>           $DOCKER_NETWORK_OPTIONS \
>           $ADD_REGISTRY \
>           $BLOCK_REGISTRY \
>           $INSECURE_REGISTRY \
>         $REGISTRIES



# Selinux

The only potential problem I can think of would be volume mounting being mislabeled.
 If you volume mount content off of the host with                                       -v /source/path:/dest/path:Z 
 and SELinux is disabled, docker will not set up labels for the container. 
 If you later turn on --selinux-enabled these containers would not be able 
 to read/write the content in the mounted volume. Thinking about this further, 
 I am not sure what labels the containers created while --selinux-disabled was set