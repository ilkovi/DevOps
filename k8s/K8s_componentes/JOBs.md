
#### JOBs

A Job is container that is run once , do its job and exists with status 0
( until successful completion )



### Job Objct

The Job object coordinates running a number of pods in parallel

If the Pod fails before a successful termination, the Job controller will create a
new Pod based on the Pod template in the Job specification


This Job pattern is defined by two primary attributes of a
Job, namely the 
* number of Job completions 
* number of Pods to run in parallel

Types:
* one shot   completions 1 , parallelism 1
* Parralel fixe completions  completetions 1+ , parallelism 1+
* Work queue	completetions 1 , parallelism 2+




### One shot

best way is to use comman-line tool

$ kubectl run -i oneshot \
--image=gcr.io/kuar-demo/kuard-amd64:1 \
--restart=OnFailure \
--  --keygen-enable \
    --keygen-exit-on-complete \
    --keygen-num-to-gen 10


NOTE:
-i interactive command. Wait util job is running and show log output
   	In this case the only POD in the JOB

--restart=OnFailure - this tells K8s to create a job object

-- all after this option are command-line argumets to the container image
	

The container then stops
$kubectl get jobs - will not show anything except -a is specified

Delete the Job before continuing:

$ kubectl delete jobs oneshot





### Using YAML

apiVersion: batch/v1
kind: Job
metadata:
  name: oneshot
  labels:
    chapter: jobs
spec:
  template:
    metadata:
      labels:
        chapter: jobs
    spec:
      containers:
      - name: kuard
	image: gcr.io/kuar-demo/kuard-amd64:1
	imagePullPolicy: Always
	args:
	- "--keygen-enable"
	- "--keygen-exit-on-complete"
	- "--keygen-num-to-gen=10"
      restartPolicy: OnFailure




# Submit the job

$ kubectl apply -f job-oneshot.yaml
job "oneshot" created


# Describe the jog

$ kubectl describe jobs oneshot


# review the result from the job

$ kubectl logs oneshot-4kfdt



### POD failure

if a pod failed. Exit with non 0 status

$ kubectl get pod -a -l job-name=oneshot

NAME 		READY 	STATUS 			RESTARTS AGE
oneshot-3ddk0 	0/1 	CrashLoopBackOff 	4 	 3m



Restart count raises.
This is because we have set :

	restartPolisy: OnFailure

If we change this to 

	restartPolicy: Never

We could end up with bunch of failed containers. Because K8s
will not restart a failed container but create a new one.
!! THIS IS DANGEROUS 






#### What if POD application failed and could not Pregress futher

To avoid this we must add a liveness probes with the Job

So if liveness check (application) failes, the POD will be restarted







#### PARALLELISM


We can configure it by:

apiVersion: batch/v1
kind: Job
metadata:
  name: parallel
  labels:
    chapter: jobs
spec:
  parallelism: 5
  completions: 10
  template:
    .....


# run the job

$ kubectl apply -f job-parallel.yaml
job "parallel" created


# Now watch as the pods come up, do their thing, and exit
--watch flag

$ kubectl get pods -w



# Clean up

$ kubectl delete job parallel









### Work Queues

In this scenatio we have a work queue 

A JOB can run to procees work item until queue is empty


Just start a kuad container with component: queue

Then expose it via service

Insert tasks into the queue

The start a JOB with component: consumer
