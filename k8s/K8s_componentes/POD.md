### PODs

( group of whales ) kitove

- It is like vApp in vmware

- one pod on one node
- kube schedule make sure two pods are on two nodes ( for redundancy )

- MYsql and Wordpress are anipatern for combination in Pod
- Good example are Web Server and Git Synch Container ( they share info via local shared filesystem )

- right question is, “Will these containers work correctly if they land on different machines?” 
	- “no,” a Pod is the correct grouping for the containers. 
	- “yes,” multiple Pods is probably the correct solution.


# Create POD
$ kubectl run kuard --image=gcr.io/kuar-demo/kuard-amd64:1

or 

$ docker run -d --name kuard \
--publish 8080:8080 \
gcr.io/kuar-demo/kuard-amd64:1

# Get POD
$ kubectl get pods

# Delete POD
$ kubectl delete deployments/kuard




### POD could be created by YAML manifest file
vi kuard-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:1
      name: kuard
      ports:
        - containerPort: 8080
        name: http
        protocol: TCP

To run it , just use: $ kubectl apply -f kuard-pod.yaml






#### Printing information with kubectl

kubectl -o (wide) json / yaml


### Build in port forwarding

$ kubectl port-forward kuard 8080:8080

a secure tunnel is created from your local machine, through the Kubernetes
master, to the instance of the Pod running on one of the worker nodes.






### Logs

$ kubectl logs kuard

-f stream log
--previous - get logs from a previous instance of the container




### Liveness Probe - if the application is serving

livenessProbe:
httpGet:
path: /healthy
port: 8080
initialDelaySeconds: 5
timeoutSeconds: 1
periodSeconds: 10
failureThreshold: 3


in the manifest file


### Readiness helthchecks - check if container is ready to server user requests

- similar to liveness
- check if container is ready



### tcpSocket  - check if a TCP socket is opened

- implement exec custom scripts
  if port opened is not enough
  Check some logic




#### Resources

- requests - minimum amount to run application
- limits   - maximum amount an application can consume



CPU and Mem , but there are others like GPU ..

They are defined in the manifest
resources:
  requests:
    cpu: "500m"
    memory: "128Mi"
  limits:
    cpu: "1000m"
    memory: "256Mi"







#### Volumes

- cache - keep some date regardless of container resart
- persistant data 
- local file system
- persistant data using remote disks
  ( NFS , iSCSI )

volumes:
   - name: "kuard-data"
     nfs:
       server: my.nfs.server.local
       path: "/exports"






