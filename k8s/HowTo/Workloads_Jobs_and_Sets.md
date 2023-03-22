
# Managing Specialized Workloads

Running a Batch Job

You want to run a process that runs for a certain time to completion, 
such as a batch conversion, backup operation, or database schema upgrade.


apiVersion:          batch/v1
kind:                Job
metadata:
  name:              counter
spec:
  template:
    metadata:
      name:          counter
    spec:
      containers:
      - name:        counter
        image:       busybox
        command:
         - "sh"
         - "-c"
         - "for i in 1 2 3 ; do echo $i ; done"
      restartPolicy: Never






# Running a Task on a Schedule Within a Pod

You want to run a task on a specific schedule within a pod managed by Kubernetes.

apiVersion:              batch/v2alpha1
kind:                    CronJob
metadata:
  name:                  hourly-date
spec:
  schedule:              "0 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name:        date
            image:       busybox
            command:
              - "sh"
              - "-c"
              - "date"
          restartPolicy: OnFailure







# Running Infrastructure Daemons per Node

You want to launch an infrastructure daemon—for example, 
a log collector or monitoring agent—making sure that exactly
 one pod runs per node.

Use a DaemonSet to launch and supervise the daemon process.


kind:                 DaemonSet
apiVersion:           extensions/v1beta1
metadata:
  name:               fluentd
spec:
  template:
    metadata:
      labels:
        app:          fluentd
      name:           fluentd
    spec:
      containers:
      - name:         fluentd
        image:        gcr.io/google_containers/fluentd-elasticsearch:1.3
        env:
         - name:      FLUENTD_ARGS
           value:     -qq
        volumeMounts:
         - name:      varlog
           mountPath: /varlog
         - name:      containers
           mountPath: /var/lib/docker/containers
      volumes:
         - hostPath:
             path:    /var/log
           name:      varlog
         - hostPath:
             path:    /var/lib/docker/containers
           name:      containers




$ kubectl describe ds/fluentd
Name:           fluentd
Selector:       app=fluentd
Node-Selector:  <none>
Labels:         app=fluentd
Annotations:    <none>
Desired Number of Nodes Scheduled: 1
Current Number of Nodes Scheduled: 1
Number of Nodes Scheduled with Up-to-date Pods: 1
Number of Nodes Scheduled with Available Pods: 1
Number of Nodes Misscheduled: 0
Pods Status:    1 Running / 0 Waiting / 0 Succeeded / 0 Failed
...












# Managing Stateful and Leader/Follower Apps


You want to run an app that requires that its pods have distinct, 
potentially different characteristics, such as a database where 
you have a leader handling reads and writes and several followers that only serve reads. 



Solution

Use a StatefulSet, which enables workloads with unique network names, 
graceful deployment/scaling/termination, or persistent storage. 


Originally, what is now known in Kubernetes as a StatefulSet was called a PetSet. This should 
give you an idea of what the motivation was. In Kubernetes 1.7, the StatefulSet became a beta 
feature, which means that the API is not going to change anymore; only UX fixes are to be expected.
 A StatefulSet is a controller that provides unique identities to the pods it is supervising.
 Note that, as a safety measure, deleting a StatefulSet will not delete the volumes associated with it.

Another use case for a StatefulSet, found quite often in the wild, is to run an app that
 was not written with Kubernetes in mind. Such apps are sometimes called legacy apps,
 from a Kubernetes perspective. We will refer to these apps as non-cloud-native apps 
going forward. Using StatefulSet is a good way to supervise such an app.

https://kubernetes.io/docs/tutorials/stateful-application/basic-stateful-set/




















# Influencing Pods’ Startup Behavior

Your pod depends on some other service being available in order to function properly

Use init containers to influence the startup behavior of a pod


First, create the backend service the web server depends on:

$ kubectl run backend --image=mhausenblas/simpleservice:0.5.0
deployment "backend" created

$ kubectl expose deployment backend --port=80 --target-port=9876




Then

Then you can use the following manifest, nginx-init-container.yaml, 


kind:                 Deployment
apiVersion:           apps/v1beta1
metadata:
  name:               nginx
spec:
  replicas:           1
  template:
    metadata:
      labels:
        app:          nginx
    spec:
      containers:
      - name:         webserver
        image:        nginx
        ports:
        - containerPort: 80
      initContainers:
      - name:         checkbackend
        image:        busybox
        command:      ['sh', '-c', 'until nslookup backend.default.svc; do echo
                       "Waiting for backend to come up"; sleep 3; done; echo
                       "Backend is up, ready to launch web server"']

