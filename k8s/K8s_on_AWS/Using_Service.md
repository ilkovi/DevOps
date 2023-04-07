

Service could be of type:

ClusterIP
NodePort
LoadBalancer


#### Setting the Environment

1. Create AWS EC2 instance from Amazon

ssh -i "docker.pem" ec2-user@107.23.131.161

2. Create a CoreOS AWS CloudFormation for a Kubernetes cluster. Add an A record for the cluster
controller instance to the public DNS name for the CloudFormation and SSH log in to the controller
instance:

ssh -i "kubernetes-coreos.pem" core@52.203.239.87

3. Install kubectl binaries and list the nodes:



#### Creating a ClusterIP Service

./kubectl run hello-world --image=tutum/hello-world --replicas=3 --port=80

./kubectl get deployments

./kubectl get pods

./kubectl expose deployment hello-world --port=80 --type=ClusterIP

./kubectl get services

./kubectl describe svc hello-world



To invoke the service in a web browser:
1. set port forwarding from a local machine.
2. Copy the keypair used to access the cluster controller instance to the KUBERNETES MASTER:

Local_MACHINE@root$ scp -i docker.pem ec2-user@EC2_INSTANCE:~/kubernetes-coreos.pem ~/kubernetes-coreos.pem

2. Then set port forwarding
 from the KUBERNETES MASTER locahost:80
 to the cluster IP of the service on the controller instance:

Local_MACHINE@root$ ssh -i kubernetes-coreos.pem -f -nNT -L 80:10.3.0.234:80 core@KUBERNETES MASTER


Port forwarding from local machine localhost:80 to cluster-ip:80 is set. Invoke the service in a web
browser at http://localhost,





#### Creating a NodePort Service

1. Delete the service

./kubectl delete svc hello-world

2. Then expose the deployment hello-world as a service of type NodePort

./kubectl expose rc hello-world --port=80 --type=NodePort


To invoke the service in a web browser, we don’t need to set port forwarding.
Obtain the public DNS name of the node at which to invoke the service as shown in Figure

Invoke the service in a web browser at URL http://public-dns:32624

Similarly, obtain the public-dns for another worker node. Invoke the service at
http://public-dns:node-port









#### Creating a LoadBalancer Service

1. Delete the serice

2. Create the service by exposing it of type LoadBalancer

./kubectl expose deployment hello-world --port=80 --type=LoadBalancer


The service is exposed as:
ClusterIP
NodePort
ExternalIP . Get it :

Obtain the external IP, the LoadBalancer Ingress, at which the service is exposed with the following
command:

./kubectl describe services hello-world | grep "LoadBalancer Ingress"



NOTE:
An AWS load balancer is created for a service of type LoadBalancer. T
he LoadBalancer Ingress is the public DNS of the load balancer, as shown in the EC2 Console










