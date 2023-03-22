

### Intergarting Storage Solutions

PERSISTANT VOLUME is
!! a storage location that has a liftime independent of any Pod or container 



# CREATE Persistan Volume

apiVersion: v1
kind: PersistentVolume
metadata:
  name: database
  labels:
spec:
  capacity:
    storage: 1Gi
  nfs:
    server: 129.168.0.1
    path: "/exports"


`NOTE:
This defines an NFS persistent volume object with 1 GB of storage space

$ kubectl apply -f nfs-volume.yaml




!! IMPORTANT
After persistent storage is created.
Claim persistent volue for POD is done via PersistentVolumeClaim Object


kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: database
spec:
  resources:
    requests:
      storage: 1Gi
    selector:
      matchLabels:
        volume: my-volume

! The selector field uses lablels to find the matching voume we defined above.











# DYNAMIC VOLUME PROVISIONING

With DVP, cluster operator creates one or more StorageClass Objects.

$ vi storageclass.yaml
apiversion: storage.k7s.io/v1beta1
kind: StorageClass
metadata:
  name: default
  annotations:
    storageclass.beta.kubernetes.io/is-defaul-class: "true"
  labels:
    kubernets.io/cluster-service: "true"
  provisionier: kuberntes.io/azure-disk


!! IMPORTANT
Once a storage class has been created for a cluster.

You can use it in the PVC - Persistent volume Claim

Dynamic provisioner seen the claim and uses the appropriate volume driver to

	- create the volume

	- bind to your PVC ( POD )


$ vi  dynamic-volume-claim.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: my-claim
  annotations:
    volume.beta.kuberntes.io/storage-class: default
  spec:
    accessModes:
    - ReadWriteOnce
    resources:
      requests:
        storage: 10Gi

IMPORTANT
The volume.beta.kubernetes.io/storage-class annotation is what links this 
claim back up to the storage class we created.














Persistent volumes are great for traditional applications that require storage.

But if you need to develop high-availability, scalable storage in

kubernetes native fashion, newly released StatefulSet object can be used.




### STATEFULSET ( kubernetes native storage ) Kubernetes 1.5 onwords


StatefulSet are replicated groups of Pods similar to ReplicaSets.

But unlike the ReplicaSets ther have certain unique properties

* each replica gets a presistnt hostname with unique indes
  ( database-0, database-1, etc. )

* each replica is creted in order from lowest to highest index
  ( creation will block until the Pod oat previous index is healthy and available )

* when deleted, each replica will be deleted in order from highest to lowest
  ( this applies to scaling down the number of replicas. )












Manually Replicated MongoDb with StatefulSets

$ vi mongo-simple.yaml

apiVersion: apps/v1beta1
kind: StatefulSet
metadata:
  name: mongo
spec:
  serviceName: "mongo"
  replicas: 3
  template:
    metadata:
      labels:
        app: mongo
      spec:
...


StatefulSets are the same as ReplicaSet, the only changes are:
apiVersion and kind


$ kubectl apply -f mongo-simple.yaml



Similarities ENDs here

$ kubectl get pods 
NAME		STATUS
mongo-0		Running
monog-1		ContainerCreating

NOTE:
First: each replicated Pod  has an index (0,1...)

Second: Pods are slowly created one after another





Create HEADLESS SERVICE
Headless service is called if it doesn't have a cluster virtual IP address
..
clusterIP: None
..

$ vi mongo-headless-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo
spec:
  ports:
  - port: 27017
    name: peer
  clusterIP: None
  selector:
    app: mongo




NOTE
We are using headless servcie because in StatefulSet each POD has
its own unique identity ( hostnames mongo-0, mongo-1 ... )


Once the headless service is created:
it creates as usual A record in the DNS
"mongo.default.svc.cluster.loca."

But when you resolve it, it returns all the addresses in the StatefulSet


In additon a records are created for
mongo-0.mongo.defalult.svc.cluster.local
mongo-1.mongo.defalult.svc.cluster.local


Each of these resolvs to a specific IP address of the replica index.











Write multiple objects in the same YAML

Or if you want, you can combine them all into a single YAML file where the
individual objects are separated by ---.








IMPORTANT
Persitent Volume Claim Template

it is like a POD template, but

instead of creating Pods it creates volume claims






Add this in the end ov the StatefulSet Definition

...
volumeClaimTemplates:
- metadata:
    name: database
    annotations:
      volume.alpha.kubernetes.io/storage-class: anything
  spec:
    accessModes: [ "ReadWriteOnce" ]
    resources:
      requests:
        storage: 100Gi



When you add a volume claim template to a StatefulSet definition, 
each time the StatefulSet controller creates a Pod that is part 
of the StatefulSet it will create a persistent volume claim based
 on this template as part of that Pod.




