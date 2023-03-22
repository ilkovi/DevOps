


### How service works

# Endpoints


Some applications want to be able to use services without using Cluster IP
Kubernets creates a buddy Endpoints object that contains the IP addresses 
for that service

$ kubectl describe endpoints alpaca-prod

To use a service, an advanced application can talk to the K8s API directly
to look up endpoints and call them.

The K8s API even has the capability to "watch" objects and be notified
as soon as they changed. In this way a client can react immediatelly as 
soon as the IPs associated with a service change!


$ kubectl get endpoints alpaca-prod --watch

# Recreate the pod

$ kubectl delete deployment alpaca-prod
$ kubectl run alpaca-prod \
--image=gcr.io/kuar-demo/kuard-amd64:1 \
--replicas=3 \
--port=8080 \
--labels="ver=1,app=alpaca,env=prod"


Output from "watch" will look like

NAME ENDPOINTS AGE
alpaca-prod 10.112.1.54:8080,10.112.2.84:8080,10.112.2.85:8080 1m
alpaca-prod 10.112.1.54:8080,10.112.2.84:8080 1m
alpaca-prod <none> 1m
alpaca-prod 10.112.2.90:8080 1m
alpaca-prod 10.112.1.57:8080,10.112.2.90:8080 1m
alpaca-prod 10.112.0.28:8080,10.112.1.57:8080,10.112.2.90:8080 1m







### Manual Service Discovery

!! Kubernetes services are built on top of lable selectors over pods 

$ kubectl get pods -o wide --show-labels

$ kubectl get pods -o wide --selector=app=alpaca,env=prod






#### Kube-proxy and Cluster IPs

NOTE: The Kubernetes service address range is configured using the --service-cluster-ip-range
flag on the kube-apiserver binary. The service address range should not overlap with the IP
subnets and ranges assigned to each Docker bridge or Kubernetes node.
In addition, any explicit cluster IP requested must come from that range and not already be in
use.


How it works:

KUBE-PROXY watches for new services in the cluster via the API server.
IF there is a new service:
- set ao IPTABLES rules in the kernel of the host
( rewrite the destination of packets so they are directed at one of the endpoints for that service )

IF the set of endopints for a service changed:
( pods comming and going or due to a failed readiness check )
- IPTABLES rules are rewritten



The cluster IP itself isusually assigned by the API server as the service is created.

When creating the service , the user can specify a specific cluser IP.

Once set, the cluster IP cannot be modified without deleting the Service Object.







#### SUMMARY

Service Types:

ClusterIP - access internal for the k8s cluster
NodePort  - access on the node ports
LoadBalancer - access from outside of k8s cluster


