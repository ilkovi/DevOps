

## Grafana
Another service installed by default is Grafana. This tool will give us a dashboard to view metrics on the cluster nodes. 


# Heapster 
is used to collect resource usage on the pods and nodes and stores the information in InfluxDB.



### Swagger 
(http://swagger.io/) is a tool to add a higher level of interaction and easy
discovery to an API


### kube-proxy
Kubernetes is using kube-proxy to determine the
proper pod IP address and port serving each request. Behind the scenes, kube-proxy is
actually using virtual IPs and iptables to make all this magic work.




# kube-controller-manager:
 The controller manager controls a variety of cluster
functions. Ensuring accurate and up-to-date replication is one of its vital roles.
Additionally, it monitors, manages, and discovers new nodes. Finally, it manages and
updates service endpoints.


###kube-apiserver:

 This container runs the API server. As we explored in the Swagger
interface, this RESTful API allows us to create, query, update, and remove various components of our Kubernetes cluster.



### kube-scheduler: 

The scheduler takes unscheduled pods and binds them to nodes
based on the current scheduling algorithm.



## pause: 
The Pause container is often referred to as the pod infrastructure container
and is used to set up and hold the networking namespace and resource limits for each pod.



!!For the most part, we see the same containers as our GCE cluster had. However, instead of fluentd-gcp service, we see fluentd-elasticsearch.

On the AWS provider, Elasticsearch and Kibana are set up for us. We can find the Kibana UI by using the following syntax as URL


### pods 
give us a logical group of
containers that we can then replicate, schedule, and balance service endpoints across



## Labels

Labels give us another level of categorization, which becomes very helpful in terms of
everyday operations and management. Similar to tags, labels can be used as the basis of
service discovery as well as a useful grouping tool for day-to-day operations and
management tasks.


Labels are just simple key-value pairs. You will see them on pods, replication controllers,
services, and so on. The label acts as a selector and tells Kubernetes which resources to
work with for a variety of operations. Think of it as a filtering option.

`As mentioned previously, labels are just simple key-value pairs. They are available on pods, replication controllers, services,


### Replication controllers (RCs), 
as the name suggests, manage the number of nodes that a
pod and included container images run on. They ensure that an instance of an image is being run with the specific number of copies.




### Health checks

in RC

livenessProbe:
## An HTTP health check
httpGet:
path: /status/
port: 80
initialDelaySeconds: 30
timeoutSeconds: 1

This is our core health check element.
From there, we can specify httpGet, tcpScoket, or exec. In this example, we use httpGet
to perform a simple check for a URI on our container. The probe will check the path and
port specified and restart the pod if it doesn’t successfully return.


!! Status codes between 200 and 399 are all considered healthy by the probe.


## readinessProbe:
# An HTTP health check
httpGet:
path: /status/
port: 80



## TCP checks

Kubernetes also supports health checks via simple TCP socket checks and also with
custom command-line scripts. The following snippets are examples of what both use cases
look like in the YAML file:

livenessProbe:
exec:
command:
-/usr/bin/health/checkHttpServce.sh
initialDelaySeconds:90
timeoutSeconds: 1

livenessProbe:
tcpSocket:
port: 80
initialDelaySeconds:





### Life cycle hooks or graceful shutdown


kind: ReplicationController

lifecycle:
`postStart:
httpGet:
path: http://my.registration-server.com/register/
port: 80
`preStop:
exec:
command: ["/usr/local/bin/apachectl","-k","graceful- stop"]


!! postStart runs before a pod enters its ready state. If the hook itself fails, the pod will be considered unhealthy.






### Docker network

`overlay driver.
 In order to coordinate across multiple hosts, they
must all agree on the available networks and their topologies. The overlay driver uses a
distributed key-value store to synchronize the network creation across multiple hosts.


`Weave 
provides an overlay network for Docker containers. It can be used as a plugin with
the new Docker network plugin interface, and it is also compatible with Kubernetes. Like
many overlay networks, many criticize the performance impact of the encapsulation
overhead. Note that they have recently added a preview release with 
!! Virtual Extensible LAN (VXLAN)


`Flannel - mix of overlay and VXLAN
Flannel comes from CoreOS and is an etcd-backed overlay. Flannel gives a full subnet to
each host/node enabling a similar pattern to the Kubernetes practice of a routable IP per
pod or group of containers. Flannel includes an in-kernel VXLAN encapsulation mode for
better performance and has an experimental multinetwork mode similar to the overlay
Docker plugin.


` Project Calico - used BGP between hosts
Project Calico is a layer 3-based networking model that uses the built-in routing functions
of the Linux kernel. Routes are propagated to virtual routers on each host via Border
Gateway Protocol (BGP). Calico can be used for anything from small-scale deploys to
large Internet-scale installations.
!! Additionally, it has a support for network-level ACLs to provide additional isolation and security.













### Balanced design
!! It’s important to point out the balance Kubernetes is trying to achieve by placing the IP at the pod level. 
Using unique IP addresses at the host level is problematic as the number of
containers grow. Ports must be used to expose services on specific containers and allow external communication.






### Load BAlancer

`Type LoadBalancer
The LoadBalancer type
creates an external load balancer on the cloud provider. We should note that support for
external load balancers varies by provider as does the implementation. In our case, we are
using GCE, so integration is pretty smooth. The only additional setup needed is to open
firewall rules for the external service ports.


` type of clusterIP
We won’t be able to test the service from a web browser this time.
However, we can use the handy kubectl exec command and attempt to connect from one of the other pods.


` NodePort type
This type allows us to expose a
service through the host or minion on a specific port. In this way, we can use the IP
address of any node (minion) and access our service on the assigned node port

!! you want to use your own load balancer in front of the nodes. Let’s make sure that we open those ports on GCP before we test our new service.







### endpoints object

You can see this by running a get endpoints
command as follows:
`$ kubectl get endpoints

However, there
may be times where you want to point to something outside your cluster.

!! If we want to create a service for something that is not a pod and therefore has no labels to select, we can easily do this with both a service and endpoint definition as follows:

apiVersion: v1
kind: Endpoints
metadata:
name: custom-service
subsets:
- addresses:
- IP: <X.X.X.X>
ports:
- name: http
port: 80
protocol: TCP











### Service discovery

` Environment variables,
[drawback]The system only creates variables at creation
time, so services that come online later will not be discovered or would require some additional tooling to update all the system environments.



` DNS.
Tip
If DNS is supported by your provider, but not setup, you can configure the following
variables in your default provider config when you create your Kubernetes cluster:
ENABLE_CLUSTER_DNS="${KUBE_ENABLE_CLUSTER_DNS:-true}"
DNS_SERVER_IP="10.0.0.10"
DNS_DOMAIN="cluster.local"
DNS_REPLICAS=1
With DNS active, services can be accessed in one of two forms—either the service name itself, 

<service-name>
, or a fully qualified name that includes the namespace, 

<servicename>.<namespace-name>.cluster.local. 

In our examples, it would look similar to node-js-90 or node-js-90.default.cluster.local.




### Persistent storage

This is where persistent disks (PDs), or volumes, come into play. A persistent volume
that exists outside the container allows us to save our important data across containers
outages. Further, if we have a volume at the pod level, data can be shared between
containers in the same application stack and within the same pod.


` Temporary disks - ram based 
volumes:
- name: memory-volume
emptydir:
medium: Memory


When the node gets restarted, all the files will be erased.


` Cloud volumes
(GCE persistent disks) - only be attached to machines in the same zone

volumes:
- name: gce-pd
gcePersistentDisk:
pdName: mysite-volume-1
fsType: ex



(AWS Elastic Block Store) 


nfs: This type allows us to mount a Network File Share (NFS),

gitrepo: As you might have guessed, this option clones a Git repo











### Multitenancy

resource limits or quotas currently set on the test namespace.

Tip
We can also set the current namespace by working with the context settings. First, we need
to find our current context, which is found with the following command:
$ kubectl config view | grep current-context
Next, we can take that context and set the namespace variable like the following:
$ kubectl config set-context <Current Context> --namespace=test




### Updates and Gradual Rollouts


#Application scaling


`Scaling up

Over time, as you run your applications in the Kubernetes cluster, you will find that some
applications need more resources, whereas others can manage with fewer resources.
Instead of removing the entire RC (and associated pods), we want a more seamless way to
scale our application up and down.

Thankfully, Kubernetes includes a scale command, which is suited specifically to this purpose. In our new example, we have only one replica running. You can check this with a get pods command.
`$ kubectl get pods -l name=node-js-scale
Let’s try scaling that up to three with the following command:
`$ kubectl scale --replicas=3 rc/node-js-scale
If all goes well, you’ll simply see the word scaled on the output of your terminal window


`Scal down
You can also use the scale command to reduce the number of replicas. In either case, the
scale command adds or removes the necessary pod replicas









# Rolling updates (simple blue-green deployment scenario)

The **rolling-update
command allows us to update entire RCs or just the underlying Docker image used by
each replica. We can also specify an update interval, which will allow us to update one
pod at a time and wait until proceeding to the next.


Let’s take our scaling example and perform a rolling update to the 0.2 version of our
container image. We will use an update interval of 2 minutes, so we can watch the process
as it happens in the following way:
`$ kubectl rolling-update node-js-scale --image=jonbaier/pod-scaling:0.2 --update-period="2m"


!!I recommend
specifying a new name for the updated controller to avoid confusion in your pod naming
down the line. The same update command with this method would look like this:
`$ kubectl rolling-update node-js-scale node-js-scale-v2.0 --image=jonbaier/pod-scaling:0.2 --update-period="2m"




# A/B testing

Let’s take a look at an A/B testing example. For this example, we will need to create a new service that uses **sessionAffinity. 
We will set the affinity to ClientIP, which will allow us to forward clients to the same backend pod. This is a key if we want a portion of our users to see one version while others see another:

apiVersion: v1
kind: Service
metadata:
name: node-js-scale-ab
labels:
service: node-js-scale-ab
spec:
`type: LoadBalancer
ports:
- port: 80
`sessionAffinity: ClientIP
selector:
service: node-js-scale-ab


* Transition from v2 to v3

$ kubectl scale --replicas=3 rc/node-js-scale-b
$ kubectl scale --replicas=1 rc/node-js-scale-a
$ kubectl scale --replicas=4 rc/node-js-scale-b
$ kubectl scale --replicas=0 rc/node-js-scale-a


`Tip
In the newly released version 1.1, K8s has a new “Horizontal Pod Autoscaler” construct which allows you to automatically scale pods based on CPU utilization.




# Scaling up your cluster

- GCE
In the preceding example, we left autoscaling turned off. However, there may be some
cases where you want to automatically scale your cluster up and down. Turning on
autoscaling will allow you to choose a metric to monitor and scale on.

- AWS

- Manual scalling








### Chapter 5. Continuous Delivery , page 198

`Gulp.js
Gulp.js gives us the framework to do Build as code. Similar

# Integration with continuous deployment pipeline



# Using Gulp.js with Kubernetes

- simular to nmp ( task definition )


# Integrating Jenkins with Kubernetes


!! Luckily, there is already a plugin, written by Carlos Sanchez, which allows you to run Jenkins slaves in Kubernetes’ pods


- install latest Kubernetes and Durable Task




# Chapter 6. Monitoring and Logging



# How Kuberentes uses cAdvisor, Heapster, InfluxDB, and Grafana


`cAdvisor 
is another open source project from Google, which provides various metrics on container resource use. Metrics include CPU, memory, and network statistics. There is no need to tell cAdvisor about individual containers; it collects the metrics for all containers on a node and reports this back to the kublet, which in turn reports to Heapster.


`Heapster
 is yet another open source project from Google;  Heapster runs in a container on one of the minion nodes and aggregates the data from kublet. A simple REST interface is provided to query the data.

The Heapster API is listening on port 8082, and we can get a full list of
metrics at /api/v1/metric-export-schema/.

$ curl -G <Heapster IP from describe>:8082/api/v1/metric-export-schema/


`InfluxDB
It is based on a key store package
and is perfect to store and query event or time-based statistics such as
those provided by Heapster.


`Grafana,
 which provides a dashboard and graphing interface for the data stored in InfluxDB.



# How FluentD and Grafana are used

pods starting with the words `fluentd-cloud-logging-kubernetes.
A pod like this exists on every node in our cluster and its sole purpose to
handle the processing of Kubernetes logs

`FluentD
Now we know that the fluentd-cloud-logging-kubernetes pods are sending the data to the Google Cloud, but why do we need FluentD
Simply put, FluentD is a collector. It can
be configured to have multiple sources to collect and tag logs, which are then sent to various output points for analysis, alerting, or archiving.


!! The AWS Kubernetes setup also uses FluentD, but instead forwards events to Elasticsearch.

`First, let’s see if we can find the FluentD config file:
$ kubectl exec fluentd-cloud-logging-kubernetes-minion-35ao --
namespace=kube-system—cat /etc/google-fluentd/google-fluentd.conf

Note that while we can make changes here, remember that is a running container and our
changes won’t be saved if the pod dies or is restarted. If we really want to customize, it’s
best to use this container as a base and build a new container that we can push to a
repository for later use.




# How to install and use logging tools

`GCE (StackDriver)
StackDriver is a great place to start for infrastructure in the public cloud. It is actually
owned by Google, so it’s integrated as the Google Cloud Platform monitoring service.
Before your lock-in alarm bells start ringing, StackDriver also has solid integration with
AWS. In addition, StackDriver has alerting capability with support for notification to a
variety of platforms and webhooks for anything else.



`Sysdig 
is open source and is billed as a universal system visibility tool with native support for containers.What sets it apart is the
level of detail and visibility it offers for these process and system activities. Furthermore, it has native support for containers, which gives us a full picture of our container operations. This is a highly recommended tool for your container operations arsenal.

First, we can see the process with the most network activity by issuing the following command:
$ sudo sysdig -pc -c topprocs_net
This is an interactive view that will show us a top process in terms of network activity


$ sudo sysdig -pc -c topprocs_cpu
$ sudo sysdig -pc -c topprocs_file
$ sudo sysdig -pc -c topprocs_cpu container.name=<Container Name NOT ID>




Simply type csysdig at the prompt( CLI UI )
$ csysdig
After entering csysdig, we see a real-time listing of all processes on the machine. At the bottom of the screen, you’ll note a menu with various options.




`commercial product, named Sysdig Cloud,
 which provides the advanced dashboard, alerting, and notification services




# Enter Tectonic, 
the CoreOS enterprise offering for running Kubernetes with CoreOS.







