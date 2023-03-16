


### Start ETC and Restart Docker


# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
cc93bf80a124        bridge              bridge              local
e79d20c55b23        host                host                local
308d3aca06cd        none                null                local


# docker network create -d overlay demo
a79c580d263c08f81426466312fac662456390dd3676ee5ae464f10d74bf3bc0

# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
cc93bf80a124        bridge              bridge              local
a79c580d263c        demo                overlay             global
e79d20c55b23        host                host                local
308d3aca06cd        none                null                local


### check etcd (internal store ) is working correctly

# ./etcdctl ls /docker
/docker/nodes
/docker/network


# ./etcdctl ls /docker/nodes
/docker/nodes/10.100.26.57:2375
/docker/nodes/10.100.26.9:2375


# ./etcdctl ls /docker/network/v1.0/network
/docker/network/v1.0/network/a79c580d263c08f81426466312fac662456390dd3676ee5ae464f10d74bf3bc0

### PLEASE NOTE
"The network ID a79c580d263c08f81426466312fac662456390dd3676ee5ae464f10d74bf3bc0 is
exactly the ID you see from docker network ls. So all the information is synchronized by etcd."



# ./etcdctl get /docker/network/v1.0/network/a79c580d263c08f81426466312fac662456390dd3676ee5ae464f10d74bf3bc0
{
 "addrSpace":"GlobalDefault",
 "enableIPv6":false,
 "generic":
  {
    "com.docker.network.enable_ipv6":false,
    "com.docker.network.generic":{}
  },
 "id":"a79c580d263c08f81426466312fac662456390dd3676ee5ae464f10d74bf3bc0",
 "inDelete":false,
 "ingress":false,
 "internal":false,
 "ipamOptions":{},
 "ipamType":"default",
 "ipamV4Config":"[{\"PreferredPool\":\"\",\"SubPool\":\"\",\"Gateway\":\"\",\"AuxAddresses\":null}]",
 "ipamV4Info":"[
                {\"IPAMData\":\"
                                {\\\"AddressSpace\\\":\\\"GlobalDefault\\\",
                                 \\\"Gateway\\\":\\\"10.0.0.1/24\\\",
                                 \\\"Pool\\\":\\\"10.0.0.0/24\\\"
                                }\",
                 \"PoolID\":\"GlobalDefault/10.0.0.0/24\"
                }
               ]",
 "labels":{},
 "name":"demo",
 "networkType":"overlay",
 "persist":true,
 "postIPv6":false,
 "scope":"global"
}







#### When we start two containers on node1 and node2 
etcd exchage information aobut Container name, MAC and IP address
NOTE: you could not start container with the same name on the other node:


host-1 # docker run -d --name test1 --net demo busybox sh -c "while true; do sleep 3600; done"
2afd3d51520e5779d98e559c863ed20728c7d124bf5c68052feafe93642aa93d


host-2 # docker run -d --name test1 --net demo busybox sh -c "while true; do sleep 3600; done"
/usr/bin/docker-current: Error response from daemon: Conflict. The name "/test1" is already in use
 by container dc9c729e29cb6b7d85d2c13d6a54c1716672e77384001f11caa806df361b162d. 
 You have to remove (or rename) that container to be able to reuse that name..
See '/usr/bin/docker-current run --help'.


host-2 # docker run -d --name test2 --net demo busybox sh -c "while true; do sleep 3600; done"
ff97647cc60b2f535b67b71b49e4a07cab08040eebec926e527e0844e9e53562


host-2 # ./etcdctl ls /docker/network/v1.0/
/docker/network/v1.0/network
/docker/network/v1.0/endpoint
/docker/network/v1.0/ipam
/docker/network/v1.0/idm
/docker/network/v1.0/overlay
/docker/network/v1.0/endpoint_count

host-2 # ./etcdctl ls /docker/network/v1.0/endpoint
/docker/network/v1.0/endpoint/5c8fed284b381b8d7e7ec5a6e091dd88d63e0ed9c4ce3091e232a8fbff3fdf7f
/docker/network/v1.0/endpoint/e79d20c55b23eadc2bfe78b6ba10cd5ddffa2f8936917f2959051ea2cd8056a7
/docker/network/v1.0/endpoint/48cb410009c38137c0b6232b1391aecc54da88582ad36ff85b7a47acf43cd44d
/docker/network/v1.0/endpoint/cc93bf80a12404a9f526b309e071f0b7517bfd021c407754fa8417c4804c388e
/docker/network/v1.0/endpoint/a79c580d263c08f81426466312fac662456390dd3676ee5ae464f10d74bf3bc0
/docker/network/v1.0/endpoint/83286b3c012e0fb956eaa2323ada562fdd075d147bbbd3182c4fe10698c60f56
/docker/network/v1.0/endpoint/308d3aca06cdef177d789075a9655d03ff78f0a5263eb28db756c8778d974496
/docker/network/v1.0/endpoint/afb67dd0d98cb5d19477ab38f9f3c993e6f317518572b146dcbbaeb9faf8d388
/docker/network/v1.0/endpoint/b88a85898a77dddad9a0ec95d3c88014613e45a8f451ef0fd96004b9dc30fbc2
/docker/network/v1.0/endpoint/5e890d18fdaeee065d96cad133199cd03fc75a29611f4415963da9ca7eb6d08a


host-2 # ./etcdctl get /docker/network/v1.0/endpoint/a79c580d263c08f81426466312fac662456390dd3676ee5ae464f10d74bf3bc0/a5c77b3935a30a62d01f0959f36ba22ebd28f99584131b82d013014cd212692c
{
"anonymous":false,
"disableResolution":false,
"ep_iface":{
            "addr":"10.0.0.2/24",
            "dstPrefix":"eth",
            "mac":"02:42:0a:00:00:02",
            "routes":null,
            "srcName":"veth44a5f8e",
            "v4PoolID":"GlobalDefault/10.0.0.0/24",
            "v6PoolID":""
           },
 "exposed_ports":[],
 "generic":{
            "com.docker.network.endpoint.exposedports":[],
            "com.docker.network.portmap":[]
           },
 "id":"a5c77b3935a30a62d01f0959f36ba22ebd28f99584131b82d013014cd212692c",
 "ingressPorts":null,
 "joinInfo":{
             "StaticRoutes":null,
             "disableGatewayService":false
            },
 "locator":"10.100.26.9",
 "myAliases":["2afd3d51520e"],
 "name":"test1",
 "sandbox":"4a43645c80dbf97dc5a697f2c11e5867c37c47a7554f63653a643f93099ec0ac",
 "svcAliases":null,
 "svcID":"",
 "svcName":"",
 "virtualIP":"\u003cnil\u003e"
}

host-2 # ./etcdctl get /docker/network/v1.0/endpoint/a79c580d263c08f81426466312fac662456390dd3676ee5ae464f10d74bf3bc0/a06cef01e9f301d8350fcf6588f9800ce9bff6e0662393b6ad87fcb9189e2aa6
{
"anonymous":false,
"disableResolution":false,
"ep_iface":{
            "addr":"10.0.0.3/24",
            "dstPrefix":"eth",
            "mac":"02:42:0a:00:00:03",
            "routes":null,
            "srcName":"vethb6f1050",
            "v4PoolID":"GlobalDefault/10.0.0.0/24",
            "v6PoolID":""
           },
"exposed_ports":[],
"generic":{
            "com.docker.network.endpoint.exposedports":[],
            "com.docker.network.portmap":[]
          },
"id":"a06cef01e9f301d8350fcf6588f9800ce9bff6e0662393b6ad87fcb9189e2aa6",
"ingressPorts":null,
"joinInfo":{
            "StaticRoutes":null,
            "disableGatewayService":false
           },
"locator":"10.100.26.57",
"myAliases":["ff97647cc60b"],
"name":"test2",
"sandbox":"ea7990833b4f96b663602e772d62a7c2da1773af2576220a0e44c1b2101ed83b",
"svcAliases":null,
"svcID":"",
"svcName":"",
"virtualIP":"\u003cnil\u003e"
}









