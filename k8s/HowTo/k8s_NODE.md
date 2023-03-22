### 3 processess that forms Kubernetes on the node

kubelet

•Reporting node and pod statuses to the API server.
•Periodically executing liveness probes.
•Mounting the pod volumes and downloading secrets.
•Controlling the container runtime (see the following).


Container runtime

•This is responsible for downloading container images and running the containers. 

kube-proxy

•This process dynamically configures iptables rules on the node to 
enable the Kubernetes service abstraction (redirecting the VIP to the
 endpoints, one or more pods representing the service).
