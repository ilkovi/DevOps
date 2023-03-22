### Service Discovery

DNS and SVR




### Service Object

Real service discovery in Kubernetes starts with a Service object.
A Service object is a way to create a named label selector. As we will see, the
Service object does some other nice things for us too


$ kubectl run alpaca-prod \
--image=gcr.io/kuar-demo/kuard-amd64:1 \
--replicas=3 \
--port=8080 \
--labels="ver=1,app=alpaca,env=prod"

$ kubectl expose deployment alpaca-prod

$ kubectl run bandicoot-prod \
--image=gcr.io/kuar-demo/kuard-amd64:2 \
--replicas=2 \
--port=8080 \
--labels="ver=2,app=bandicoot,env=prod"

$ kubectl expose deployment bandicoot-prod

$ kubectl get services -o wide
NAME CLUSTER-IP ... PORT(S) ... SELECTOR
alpaca-prod 10.115.245.13 ... 8080/TCP ... app=alpaca,env=prod,ver=1
bandicoot-prod 10.115.242.3 ... 8080/TCP ... app=bandicoot,env=prod,ver=2


### Furthermore, that service is assigned a new type of virtual IP called a cluster IP.


$ ALPACA_POD=$(kubectl get pods -l app=alpaca \
-o jsonpath='{.items[0].metadata.name}')

$ kubectl port-forward $ALPACA_POD 48858:8080







#### Service DNS

-The k8s DNS service provides DNS names for cluster IPs

- Within a namespace, it is as easy as just using the service name to 
connect to one of the pods identified by a service.


If you resolve one of the pods, you will get something like:

alpaca-prod.default.svc.cluster.local

- alpaca-prod 	= the name of the service in question
- default 	= the namespace that this service is in
- svc		= recognizeing that this is a service






#### Endpoints

- are lower-level way of finding twhat a service is sendingn traffic to

--watch = causes the kubectl to hang around and output any updates

$ kubectl get endpoints alpaca-prod --watch


If you add readiness check to a pod,
you will see that

"Only ready pods are sent traffic"

If readiness check failed K8s remove pod from the list of endpints for the service







#### NodePort

you can expose service to a node port ( on every node )
so every node in the cluster  then forwards traffic to that port to the service

This can be integrated with hardware or software load balancers to expose the service further.

In the yaml manifest for the deployment, just specify
--type=NodePort


If you are sitting on the same netowrk, you can access it directly.
If your cluster is in the cloud, you can use SSH tunneling:

$ ssh <node> -L 8080:localhost:32711

now open your browser to htt://localhost:8080





#### LoadBalancer

Edit the pod service and change 
spec.type to LoadBalancer

Run it and check with describe

$ kubectl describe service alpaca-prod

Check the line : LoadBalancer Ingress: ...IP...


IF you have support form the cloud that you are running !

This builds on NodePorts by additionally configuring the cloud to crate a new load ballancer
and direct it at nodes in your cluster.



The way that LoadBalancer works is specific to a cloud.
Some clouds have DNS-basedload balancers ( AWS ELB )
in that case you will have hostname

