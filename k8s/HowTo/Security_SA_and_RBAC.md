
### The role and usage of service accounts


### Create a Service Account

$ kubectl create serviceaccount myappsa


# Describe the SA

$ kubectl describe sa myappsa	
..
Mountable secrets:      myappsa-token-rr6jc
Tokens:                 myappsa-token-rr6jc


# View the SA secret

$ kubectl describe secret myappsa-token-rr6jc
..
Data
====
ca.crt:         1066 bytes
namespace:      7 bytes
token:          eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9


# Use SA in a Container

kind:                 Pod
apiVersion:           v1
metadata:
  name:               myapp
spec:
  serviceAccountName: myappsa
  containers:
...


# Verify that the SA is used

$ kubectl exec myapp -c main \
          cat  /var/run/secrets/kubernetes.io/serviceaccount/token
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9 ...


NOTE
SA is automatically mounted under:
/var/run/secrets/kubernetes.io/serviceaccount/token

IMPORTANT
While a service account on its own is not super useful, 
it forms the basis for fine-grained access control


Authentication managment is done via this token.
/var/run/secrets/kubernetes.io/serviceaccount/token, 
which is mounted automatically through a secret.





















### Role-Based Access Control (RBAC)


The service accounts are namespaced resources and are represented as follows
system:serviceaccount:$NAMESPACE:$SERVICEACCOUNT


### Listing the service accounts in a certain namespace 

$ kubectl get sa


### Check if account have enought permissions

$ kubectl auth can-i list pods --as=system:serviceaccount:app-storage:heketi-storage-service-account -n app-storage
yes

$ kubectl auth can-i list secrets --namespace dev --as dave
no

$ kubectl auth can-i create deployments --namespace dev
yes

$ kubectl auth can-i create deployments --namespace prod
no




### GET ROLES

$ kubectl get roles -n=kube-system



### GET CLUSTER ROLES

$ kubectl get clusterroles -n=kube-system

- you can use the predefined roles directly for users and service accounts.


$ kubectl describe clusterroles/view -n=kube-system





### ILUSTRATION


ENTITY 	- user, group or service account

RESOURCE - pod, service or secret

ROLE 	- rules for actions on a resource ( get, list, create, update, delete .. )
	- can only be used to grant access to resources within a single namespace

CLUSTERROLE - same like Role, but on Cluster level
	- cluster-scoped resources (like nodes)
	- non-resource endpoints (like “/healthz”)
	- namespaced resources (like pods) across all namespaces

ROLE BINDING 	- links Role to Entity
	- A role binding grants the permissions defined in a role to a user or set of users
	- A RoleBinding may also reference a ClusterRole to grant the permissions to namespaced 	  resources defined in the ClusterRole

ClusterRoleBinding - cluster-wide
	




### Example of adding a role 

kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]



### Example of adding a CLUSTER ROLE

 Grant read access to secrets in any particular namespace

kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  # "namespace" omitted since ClusterRoles are not namespaced
  name: secret-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]






### Example ROLE reffering a Resouce

Most resources are represented by a string representation of their name, 
such as “pods”, just as it appears in the URL for the relevant API endpoint

kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: default
  name: pod-and-pod-logs-reader
rules:
- apiGroups: [""]
  resources: ["pods", "pods/log"]
  verbs: ["get", "list"]






NOTE
Link between http request and kuberntes requests


HTTP verb       request verb

POST            create 
GET, HEAD       get (for individual resources), list (for collections) 
PUT             update 
PATCH           patch 
DELETE          delete (for individual resources), deletecollection (for collections) 





















### Defining a pod’s security context


To enforce policies on the pod level in Kubernetes, use the securityContext field in a pod specification

kind:                Pod
apiVersion:          v1
metadata:
  name:              secpod
spec:
  containers:
  - name:            shell
    image:           centos:7
    command:
      - "bin/bash"
      - "-c"
      - "sleep 10000"
    securityContext:
      runAsUser:     5000

Now create the pod and check the user under which the container runs:

$ kubectl create -f securedpod.yaml
pod "secpod" created

$ kubectl exec secpod ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
5000 




A more powerful method to enforce policies on the pod level is to use pod security policies (PSP).

“Secure a Kubernetes Cluster with Pod Security Policies”
https://docs.bitnami.com/kubernetes/how-to/secure-kubernetes-cluster-psp/



Pod Security Policies documentation
https://kubernetes.io/docs/concepts/policy/pod-security-policy/

Configure a Security Context for a Pod or Container
https://kubernetes.io/docs/tasks/configure-pod-container/security-context/






### PSP - Pod Security Policy

This resource lists the conditions a pod must meet in order to run in the cluster

# check if it is enabled
$ kubectl get psp
No resources found. 			- It is Enabled



example.yaml

apiVersion: extensions/v1beta1
kind: PodSecurityPolicy
metadata:
  name: example
spec:
  privileged: false
  runAsUser:
    rule: MustRunAsNonRoot
  seLinux:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  volumes:
  - 'nfs'
  hostPorts:
  - min: 100
    max: 100


This example does:
•Disallow containers running in privileged mode
•Disallow containers that require root privileges
•Disallow containers that access volumes apart from NFS volumes
•Disallow containers that access host ports apart from port 100


•The privileged field indicates whether to allow containers that use privileged mode
•The runAsUser field defines which users a container can run as. Most commonly, it is used to prevent pods from running as the root user.
•The seLinux field defines the Security-Enhanced Linux (SELinux) security context for containers and only allows containers that match that context
•The supplementalGroups and fsGroup fields define the user groups or fsGroup-owned volumes that a container may access
•The volumes field defines the type(s) of volumes a container may access. Learn more about volumes.
•The hostPorts field, together with related fields like hostNetwork, hostPID and hostIPC, restrict the ports (and other networking capabilities) that a container may access on the host system.



