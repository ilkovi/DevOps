#### ReplicaSet

A ReplicaSet acts as a cluster-wide Pod Manager

Like all concepts in Kubernetes, ReplicaSets are defined using a specification.
All ReplicaSets must have a unique name (defined using the metadata.name
field), a spec section that describes the number of Pods (replicas) that should be
running cluster-wide at a given time, and a Pod template that describes the Pod
to be created when the defined number of replicas is not met. Example 8-1
shows a minimal ReplicaSet definition.



apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
  name: kuard
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: kuard
        version: "2"
    spec:
      containers:
        - name: kuard
	  image: "gcr.io/kuar-demo/kuard-amd64:2"
	  ports:
	    - containerPort: 80


### RS commands

$ kubectl apply -f kuard-rs.yaml

$ kubectl describe rs kuard


$ kubectl scale kuard --replicas=4







### Finding a ReplicaSet from a Pod

$ kubectl get pods <pod-name> -o yaml






### Finding a Set of Pods for a ReplicaSet

use the --selector flag or the shorthand -l:

$ kubectl get pods -l app=kuard,version=2






#### Auto scaling
Kubernetes can handle all of these scenarios via horizontal pod autoscaling

HPA = horizontal pod autoscaling

HPA requires the presence of the heapster Pod on your cluster. heapster keeps track of
metrics and provides an API






### Autoscaling based on CPU

This command creates an autoscaler that scales between two and five replicas
with a CPU threshold of 80%.

$ kubectl autoscale rs kuard --min=2 --max=5 --cpu-percent=80



# To view the autoscaling you could use resource. horizontalpodautoscalers

or shorter case:

$ kubectl get hpa




!! WARNING

Do not use autoscaling and repilicaset

- both will try to modify the number of pods and it will crash



### Delete replica set

$ kubectl delete rs kuard

- pods will be delted as well



IF you want to delete only the replicaset definition


$ kubectl delete rs kuard --cascade=false

- this will left containers

