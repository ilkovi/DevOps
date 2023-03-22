

### DEPLOYMENTS

Deployments object exists to manage release of new version

Using Deployments you can simply and reliably roll out
new software versions without downtime or errors.

First the updates happened with

$ kubectl roling-update

Now

Deployment ojbect.






### How it works

$ kubectl run nginx --image=nginx:1.7.12


- this is a deployment. Checi it out


$ kubectl get deployments nginx






NOTE:
ReplicaSets mange Pods
Deploymentes manage ReplicaSets    via labels and lableSelector


$ kubectl get deploymnets nginx \
  -o jsonpath --template {.Spec.selector.matchLabels}


VIEW:
the Lable "run=nginx"

-Check ReplicaSet

$ kubectl get replicasets --selector=run=nginx










# We could manage the replicaSet from deployment

$ kubectl scale deplyments nginx --replicas=2

$ kubectl get replicasets --selector=run=nginx



# We coudl manage replicaset from deployment replicaset 

$ kubectl scale replicasets nginx-1128242161 --replicas=1



SUPRISE:
This will not decrease the number of the pods for deployment.

Becase,

Replicaset is tide to parent object Deployment.
that have 2 replicas.
Even that we decrese the number directly, the parent object return
replicas to 2 !!






### Create Deployment YAML

$ kubectl get deplyments nginx --export -o yaml > nginx-deployment.yaml

$ kubectl replace -f nginx-deployment.yaml --save-config

NOTE:
kubectl replace --save-config,
is used to add annotation !

So that when applying changes in the future, kubectl will
know waht the last applied configration was for smarter merging of configs.









IMPORTANT:
YAML for Deployments consist of startegy section:

..
  stargegy;
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
...

The strategy object dictetes the different ways in which a roolout of new
sofware can proceed. There are two different strategies supported by
Deployments: RECREATE and ROLLINGUPDATE









IMPORTANT:

$ kubectl describe deployments nginx

...
OldReplicaSets: 	<none>
NewReplicaSets:		nginx-1128242161 (2/2 replicas created )

Theses fields poin to ReplicaSet objectds this Deployment
is currently managing.
IF a Deployment is in th middle of rolloug, both fields will have value.
IF a rollout is complete, OldReplicaSets will be set to <none>







COMMANDS:
kubectl rollout history - view history of a rollouts

EXMPLS:
$ kubectl rollout history deployment nginx

$ kubectl rollout history deployment nginx --revison=2
  -- provide more details about a revision

$ kubectl rollout undo deployment nginx 
  -- rollback one version

$ kubectl rollout undo deployments nginx --to-revision=3



kubectl rollout status - view currnet status of a rollout





# EDIT Deployment

Best practice is to edit the yaml

...
spec:
  replicas: 3
...


$ kubectl apply -f nginx-deployment.yaml

$ kubectl get deployment nginx











# UPDATING a Conatiner Image

...
	conatiners:
	- image: nginx:1.9.10
	  imagePullolicy: Always
...

Edit Template for Deployment
...
spec:
  ..
  template:
    annotations:
      kubernetes.io/change-cause: "Update ningx to 1.9.10"
...


!! Make sure you are editing the annotation for TEMPLATE

!! Updating annotation will cause new rollout

!! Do not update annotation for replicaset changes 


$ kubectl apply -f nginx-deployment.yaml

$ kubectl rollout status deployments nginx

$ kubectl get replicasets -o wide

( both old and new containers are kept around in case of a rollback )



You can pause the RollOut:

$ kubectl rollout pause deployments nginx

Do investigation, if all good proceed:

$ kubectl rollout resume deployments nginx








### Best practice to limit the revison history
...
spec:
  revisionHisotryLimit: 14
...

14 is good for a daily builds, taht you don't want to revert more that two weeks back.









Deployment Strategies:
1. Recraete: used for test env, cause downtime
2. RollingUpdate - sophisticate and slow, updates couple of pods at the 
		same time. Do not drop a connection







RollingUpdates Parameters

maxUnavailable - how many pods to decrease from total number NUMBER or 20% percentige
maxSurge - how many pods to increase from total number
	   Need Extra Resoursec
	   Guarnatee 100 % of Pods working at a time
	   Could be percentage or number
	   maxUnavailable must me set to 0 !
	   maxSurge=100% 			* is BLUE/GREEN Deployment !






# Failed Deployment

Your Deployment may get stuck trying to deploy its newest ReplicaSet without ever completing. 
This can occur due to some of the following factors:

    Insufficient quota
    Readiness probe failures
    Image pull errors
    Insufficient permissions
    Limit ranges
    Application runtime misconfiguration






!! Note:
To take advantage of the RollUpdate
You must have readiness health check for your container applications 





NOTE:
Sometimes readiness healthcheck is not enough.
A memory leak or other issues could occure in a few minutes after deployment.
So it is good to slow your deployment by:

...
spec:
  minReadySeconds: 60
...

This tells to deployment controller to wait 60 seconds,
after a POD become ready. And then to proceed with next pod.


You could sepcify progressDeadlineSeconds.
This is a timeout period,
prevent update from stucing forever.

...
spec:
  progressDeadlineSeconds: 600
...








# Delete a deployment

$ kubectl delete deployments nginx
 - deletes the deployment, replicaset and pods

$ kubectl delete deploymnets nginx --cascade=false
 - only deployment
