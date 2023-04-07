
### K8s in Production

- many masters - for redundancy
- many nodes


### K8s on prem

- you control how many masters to have
- you control Node Pools


### K8s in GCP - GKE

- masters are managed by GOOGLE
- you have control over Node Pool
- you take care of updates ( crate new Pools, migrate everything )



### Deployoment
( include many definitions )
POD
Service 
ReplicaSet




### YAML
!! Everything in K8s is configured with YAML

`Generate 
kubectl run nginx --image=nginx --restart=Never

in the past , you could just add ** --dry-run **

Now

Run ** --dry-run=client -o yaml ** for basic yaml generation

Run ** --dry-ryn=server -o yaml ** for more detail yaml generation (or ** --dry-run=none **) 
 
NOTE: will not run any containers

NOTE: to save in file, just use ** > pod_ngingx.yaml **





### On the K8s exam
- it is very useful to generate yaml files with dry run

- you can use the officisal k8s website https://kubernetes.io/ 
( you are limited to use only one tab on the exam)




### Setup VI

:set cursorcolumn



### Networks - 4 types
- between Pods
- between Nodes
- in the cluster
- ingress ( ofen NGINX that acts as loadbalancer )
	on prem , you decide what ingress to use, but default is NGINX
	it could be HA PROXY
	GCP it is NGINX 
	
	



### Port-Forward - for troubleshooting

kubectl port-forward --address 0.0.0.0 pod/nginx 8888:80

( if you have VPN/access to K8s Node )


!! kubectl port-forward pod/nginx-app-5d47bf8b9-5hj9j 8888:80 --address='0.0.0.0'








### DIFFERENCES REAL WORLD / EXAM

- in the real world, we use DEPLOYMENT


- on the exam we use pods







### NAMESPACE

kubectl get namespaces

kubectl create namespace Dev	( short: kubectl create ns Dev )


** set namespace
kubectl config set-context --current --namespace=my-namespace


*HINT - use bash aliases
$ alias kubens='kubectl config set-context --current --namespace '
$ alias kubectx='kubectl config use-context '

or
alias kx='f() { [ "$1" ] && kubectl config use-context $1 || kubectl config current-context ; } ; f'
alias kn='f() { [ "$1" ] && kubectl config set-context --current --namespace $1 || kubectl config view --minify | grep namespace | cut -d" " -f6 ; } ; f'


// Usage
$ kubens kube-system    //Switch to a different namespace
$ kubens - 				//Switch to default ns
$ kubectx docker        //Switch to separate context



###### Contex 
A kubernetes context is just a set of access parameters 
that contains a Kubernetes cluster, a user, and a namespace. 

kubernetes Context is essentially the configuration that 
you use to access a particular cluster & namespace with a user account.



!! Use Case
If you run kubectl from the master - You don't need context 
( you are directly connected to current cluster )
( because you don't kave many clusters on the same node )


GCP
- you could not ssh to master - you use context
Context ( URL and credentials to authenticate to the cluster )
how it works in GCP
- via GCP SDK you are authenticated for the specific project
- then it is added to your kubectl context



* View The Current Context
master $ kubectl config current-context

kubernetes-admin@kubernetes


* View Context Configuration
master $ kubectl config view

- you could find the same info $HOME/.kube/KUBECONFIG OR $HOME/.kube/config 


* Cretae New Context:
kubectl config set-context dev --namespace=development --cluster=lithe-cocoa-92103_kubernetes --user=lithe-cocoa-92103_kubernetes


kubectl config set-context prod --namespace=production --cluster=lithe-cocoa-92103_kubernetes --user=lithe-cocoa-92103_kubernetes


* Switch to Different Context:
kubectl config use-context prod

master $ kubectl config current-context


* List Your Kubernetes Contexts

kubectl config get-contexts







### Search in K9s

/ 
or
:





### Certificates

There are two
Developer - it is a bit easy
Administrator - ( take a look at kube-system namespace )

50 % are the same
If you take Developer, it is good to take fast Administrator










### GCP Migration to newer version ( K8s version of worker become out of support )

1) create new work nodes

2) create new pool

3) cordon ( nothing goes to old cluster ? )
` There is downtime, because k8s shutdown old deployments and start a new one

4) drain  ( old pool )

5) delete old pool


This where GCP Cloud Run come into place
Google take care of everything. You only run pods/deployments.
They take care of scaling and redundancy.
Only StateLESS applications.










### K8s On Prem
KubeSpray - ansible palybook


### GCP / cloud
Terraform




### Real Live Situation

You inherit a K8s cluster in the cloud

It was configured with Terraform
Then someone do not know how to work with it and do thing manually.

At the moment you run terraform - NASTY things happen :( 

It is always best to use Terrafrom - fast and reliable








### DEPLOYMENT

1 ) create deployment and service YAML with --dry-run=client

kubectl create deployment nginx-app --image=nginx --replicas=2 --dry-run=client -o yaml > deployment-nginx.yaml

kubectl expose deployment nginx-app --type=NodePort --port=80 --dry-run=client -o yaml > service.yaml


2 ) cat deployment to service file 

cat deployment-nginx.yaml >> service.yaml


3 ) edit the service.yaml

separete two entries with 

"
---
"

4 ) MATCH serice to the deployment

`via LABELs and SELECTOR

` Verify SELECTOR in the service - in the specifications 
 
...service
spec:
  containers:
  ..
    ..
` selector:
`   app: nginx
  ..
  
or Check documentation https://kubernetes.io

5 ) Clear status and timestamp









### GET all configuration

kubectl get all --all-namespaces







### Run deployment and check for errors

ilkovi@master:~$ kubectl create -f service.yaml
error: error parsing service.yaml: error converting YAML to JSON: yaml: line 16: could not find expected ':'




### Scale Replica Set

ilkovi@master:~$ kubectl scale deployment nginx-app --replicas 1
deployment.apps/nginx-app scaled



### Attach to POD via kubectl

ilkovi@master:~$ kubectl exec -it nginx-app-5d47bf8b9-9g2bp -- bash
root@nginx-app-5d47bf8b9-9g2bp:/#









### HOW To TEST deployment ( if pods are working ) - busybox container

kubectl run busybox --image=busybox --rm -it --restart=Never -- wget -O- ..container_ip..:80



ilkovi@master:~$ kubectl describe pod/nginx-app-5d47bf8b9-9g2bp | grep ^IP:
IP:               10.244.12.4
ilkovi@master:~$

`or

kubectl get all -o wide




kubectl run busybox --image=busybox --rm -it --restart=Never -- wget -O- 10.244.12.4:80

ilkovi@master:~$ kubectl run busybox --image=busybox --rm -it --restart=Never -- wget -O- 10.244.12.4:80
Connecting to 10.244.12.4:80 (10.244.12.4:80)
writing to stdout
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
-                    100% |********************************|   615  0:00:00 ETA
written to stdout
pod "busybox" deleted
ilkovi@master:~$












### LOGS

if the pod is dead , use -p ( previous )


kubectl logs ....pod_name.... -p



### Troubleshoot deployment failure

- describe the deployment 
( if there is an error , it is on the bottom )

- describe the pod ( more useful )

$ kubectl describe deployment.apps/nginx-app
...
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  35m   deployment-controller  Scaled up replica set nginx-app-5d47bf8b9 to 2
  Normal  ScalingReplicaSet  29m   deployment-controller  Scaled down replica set nginx-app-5d47bf8b9 to 1 from 2
  
  
  





### Limits

--requests='cpu=50m,memory=50Mi'

` GET info about it
kubect top pods

kubect top pod ..name of the pod..






### Secrets

similar to config maps , but used to store secrets.
- certificate
- variables 

kubectl create secret nginx-cert --from-file=special=nginx.ppk








### Config MAPS

- store confiuration ( like nginx )
- store variables 


 
* Create config map from variables from terminal

kubectl create configmap config --from-literal=e=b --from-literal=c=df


* View
kubectl describe configmap config



* you can crate configmap from file

ilkovi@master:~$ echo -e "var3=val3\nvar4=val4" > config.txt
ilkovi@master:~$ kubectl create cm config --from-file=special=config.txt
configmap/config created
ilkovi@master:~$ kubectl describe cm config
Name:         config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
special:
----
var3=val3
var4=val4


BinaryData
====

Events:  <none>
ilkovi@master:~$ kubectl create cm config --from-file=special=config.txt --dry-run=client -o yaml
apiVersion: v1
data:
  special: |
    var3=val3
    var4=val4
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: config
ilkovi@master:~$



!! Same can be done with SECTRETS ( ssl certificates )












### Service Accounts

It is used in GCP , because everything is service there

**Used to access a service in GCP - you need Service account


`Deployment 
describe the service account

`Secret 
keys to authenticate to Account
which service account to use

`ServiceAccount
describe the secret







Use the default service account to access the API server

When Pods contact the API server, Pods authenticate as a particular ServiceAccount 
(for example, default). There is always at least one ServiceAccount in each namespace.

Every Kubernetes namespace contains at least one ServiceAccount: 
the default ServiceAccount for that namespace, named default. 
If you do not specify a ServiceAccount when you create a Pod, 
Kubernetes automatically assigns the ServiceAccount named default in that namespace.

You can fetch the details for a Pod you have created. For example:

kubectl get pods/<podname> -o yaml

In the output, you see a field spec.serviceAccountName. 
Kubernetes automatically sets that value if you don't specify it when you create a Pod.

`$ kubectl describe pod/nginx-app-5d47bf8b9-tjtwd | grep "Service Account"
Service Account:  default










### Storages


create Persistant Volume

creates PersistantVolume Claim

* in pods or deployment

define, the volume mount


`Read documentation. On the Exam, there are few questions about it









### Helm

Repo for YAML







### Bonux

kubect run 		- used to declare pods

--expose 		- used to create service

kubectl run nginx --image=nginx --restart=Never --expose  --port 80 --dry-run=client -o yaml

or two commands

kubectl run nginx --image=nginx --restart=Never

kubect expose pod nginx --port 80 --target-port=80 --type=NodePort


