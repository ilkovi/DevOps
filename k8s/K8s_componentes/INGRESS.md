


### What is INGRESS

Ingress, added in Kubernetes v1.1, exposes HTTP and HTTPS routes 
from outside the cluster 
to services within the cluster



Traffic routing is controlled by rules defined on the ingress resource.

    internet
        |
   [ Ingress ]
   --|-----|--
   [ Services ]



An ingress can be configured to give services 
* externally-reachable URLs, 
* load balance traffic, 
* terminate SSL, 
and offer name based virtual hosting.


An ingress controller is responsible for fulfilling the ingress, 
usually with a loadbalancer, 
though it may also configure your edge router 
or additional frontends to help handle the traffic.








# Service Types


Services exposes application via
* ClusterIP ( default )
  - This type makes the Service only reachable from within the cluster.

* NodePort ( <node>:<port> )
  -  using NAT. Makes a Service accessible from outside the cluster 
     using <NodeIP>:<NodePort>. Superset of ClusterIP.

* LoadBalancer 
   Creates an external load balancer in the current cloud (if supported)
   and assigns a fixed, external IP to the Service. Superset of NodePort.

* ExternalName
  - ExternalName in the spec) by returning a CNAME record with the name.
    No proxy is used. This type requires v1.7 or higher of kube-dns









# Epose single service with INGRESS


apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-ingress
spec:
  backend:
    serviceName: testsvc
    servicePort: 80

$ kubectl create -f

$ kubectl get ingress test-ingress
NAME           HOSTS     ADDRESS           PORTS     AGE
test-ingress   *         107.178.254.228   80        59s


NOTE
Where 107.178.254.228 is the IP allocated by the ingress controller to satisfy this ingress.

















# SIMPLE FANOUT
A fanout configuration routes traffic from a single IP address to more than one service
based on the HTTP URI being requested 
An ingress allows you to keep the number of loadbalancers down to a minimum

For Example

foo.bar.com -> 178.91.123.132 -> / foo    service1:4200
                                 / bar    service2:8080


would require an ingress such as:

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: simple-fanout-example
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: foo.bar.com
    http:
      paths:
      - path: /foo
        backend:
          serviceName: service1
          servicePort: 4200
      - path: /bar
        backend:
          serviceName: service2
          servicePort: 8080


$ kubectl create -f

$ kubectl describe ingress simple-fanout-example

Name:             simple-fanout-example
Namespace:        default
Address:          178.91.123.132
Default backend:  default-http-backend:80 (10.8.2.3:8080)
Rules:
  Host         Path  Backends
  ----         ----  --------
  foo.bar.com
               /foo   service1:4200 (10.8.0.90:4200)
               /bar   service2:8080 (10.8.0.91:8080)
Annotations:
  nginx.ingress.kubernetes.io/rewrite-target:  /
Events:
  Type     Reason  Age                From                     Message
  ----     ------  ----               ----                     -------
  Normal   ADD     22s                loadbalancer-controller  default/test



` NOTE
The ingress controller will provision an implementation specific loadbalancer that satisfies the ingress
Depending on the ingress controller you are using, you may need to create a default-http-backend Service
















Name based virtual hosting

Name-based virtual hosts support routing HTTP traffic to multiple host names at the same IP address.

foo.bar.com --|                 |-> foo.bar.com s1:80
              | 178.91.123.132  |
bar.foo.com --|                 |-> bar.foo.com s2:80

The following ingress tells the backing loadbalancer to route requests based on the Host header.









# TLS

The TLS secret must contain keys named tls.crt and tls.key that
 contain the certificate and private key to use for TLS, e.g.:

apiVersion: v1
data:
  tls.crt: base64 encoded cert
  tls.key: base64 encoded key
kind: Secret
metadata:
  name: testsecret-tls
  namespace: default
type: Opaque








# Ingress controllers

In order for the ingress resource to work, the cluster must have an ingress 
controller running. This is unlike other types of controllers, which run as
 part of the kube-controller-manager binary, and are typically started 
automatically with a cluster. Choose the ingress controller implementation 
that best fits your cluster.

`NOTE
Kubernetes as a project currently supports and maintains GCE and nginx controllers.



!! IMPORTANT
You may deploy any number of ingress controllers within a cluster.
 When you create an ingress, you should annotate each ingress with
 the appropriate ingress-class to indicate which ingress controller 
should be used if more than one exists within your cluster. 

If you do not define a class, your cloud provider may use a default ingress provider

