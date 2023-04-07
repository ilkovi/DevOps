
### Daemon Set 

A DaemonSet ensures a copy of a Pod is running across a set of nodes in a
Kubernetes cluster. DaemonSets are used to deploy system daemons such as log
collectors and monitoring agents, which typically must run on every node.
DaemonSets share similar functionality with ReplicaSets; both create Pods that
are expected to be long-running services and ensure that the desired state and the
observed state of the cluster match.


You could limit the DaemonSet scheduling pods on evry node by
using selector. That way the DaemonSet Scheuler skip the nodes
that doen't apply to selector labels


### Commands


$ kubectl apply -f fluentd.yaml
daemonset "fluentd" created

$ kubectl describe daemonset fluentd


 --- verify that each fluend run on evry node

$ kubectl get pods -o wide
NAME AGE NODE
fluentd-1q6c6 13m k0-default-pool-35609c18-z7tb
fluentd-mwi7h 13m k0-default-pool-35609c18-ydae
fluentd-zr6l7 13m k0-default-pool-35609c18-pol3



# Adding lables to the nodes


$ kubectl label nodes k0-default-pool-35609c18-z7tb ssd=true


# Check


$ kubectl get nodes --selector ssd=true

apiVersion: extensions/v1beta1
kind: "DaemonSet"
metadata:
  labels:
    app: nginx
    ssd: "true"
  name: nginx-fast-storage
spec:
  template:
    metadata:
      labels:
        app: nginx
	ssd: "true"
      spec:
	nodeSelector:
	  ssd: "true"
        containers:
	  - name: nginx
	  image: nginx:1.10.0



$ kubectl apply -f nginx-fast-storage.yaml

$ kubectl get pods -o wide


WARNING: 
Removing labels from a node that are required by a DeamonSet's node selector 
will cause the Pod being managed by that DaemonSet to be REMOVED.








#### Updating a DaemonSet by Deleting Individual Pods

KUBERNETES version prior 1.6


If you are running a pre-1.6 version of Kubernetes, you can perform a rolling
delete of the Pods a DaemonSet manages using a for loop on your own machine
to update one DaemonSet Pod every 60 seconds:


PODS=$(kubectl get pods -o jsonpath -template='{.items[*].metadata.name}'
for x in $PODS; do
  kubectl delete pods ${x}
  sleep 60
done





#### Rolling Update of a DaemonSet

With Kubernetes 1.6 and greated

To set a DaemonSet to use the rolling update strategy,
you need to configure the update strategy using the spec.updateStrategy.type
field.

There are two parameters that control the rolling
update of a DaemonSet:
* spec.minReadySeconds, which determines how long a Pod must be “ready”
before the rolling update proceeds to upgrade subsequent Pods
~ 30 or 60 seconds


* spec.updateStrategy.rollingUpdate.maxUnavailable, which indicates
how many Pods may be simultaneously updated by the rolling update
1 is good




### show the current rollout status of a DaemonSet named my-daemon-set

$ kubectl rollout status daemonSets my-daemon-set 





### Delete a DaemonSet

$ kubectl delete -f fluentd.yaml

WARNING:

Delte a DameonSet will delete all the pods manged by it.
--cascade=false to leave the Pods intact






apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
name: fluentd
namespace: kube-system
labels:
app: fluentd
spec:
template:
metadata:
labels:
app: fluentd
spec:
containers:
- name: fluentd
image: fluent/fluentd:v0.14.10
resources:
limits:
memory: 200Mi
requests:
cpu: 100m
memory: 200Mi
volumeMounts:
- name: varlog
mountPath: /var/log
- name: varlibdockercontainers
mountPath: /var/lib/docker/containers
readOnly: true
terminationGracePeriodSeconds: 30
volumes:
- name: varlog
hostPath:
path: /var/log
- name: varlibdockercontainers
hostPath:
path: /var/lib/docker/containers

