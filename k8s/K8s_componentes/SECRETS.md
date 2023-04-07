

# SECRETS are used to provide sensitive information to workloads
- credentials
- TLS certificates


!! WARNING
BAD IDEA to check secret YAML files into source control




### SECRETS

Secrets enable container images to be created without handling sensitive data

Container remain portable across envionments.

Secrets are exposed to pods via explicit declaration in pod manifest and k8s API


` NOTE:
Prior k8s v 1.6 a root user in any node could revial all secrets
That was fixed in the 1.7


# 3 Types
IT have 3 types of secrets

* docker-registry 

* gneric - create a secret from local file/directority or literal value ( based64-encode it yourself )

* tls - a secure certificate for ingress


` NOTE
Prior to Kubernetes 1.7, the API server stored secrets as plain text in etcd
Now you have the option to encrypt them using the --experimental-encryption-provider-config option when launching the kube-apiserver.





## Create a Secret

$ curl -O https://storage.googleapis.com/kuar-demo/kuard.crt
$ curl -O https://storage.googleapis.com/kuar-demo/kuard.key


$ kubectl create secret generic kuard-tls \
--from-file=kuard.crt \
--from-file=kuard.key


$ kubectl describe secrets kuard-tls
Name: kuard-tls
Namespace: default
Labels: <none>
Annotations: <none>
Type: Opaque
Data
====
kuard.crt: 1050 bytes
kuard.key: 1679 bytes








#### Consuming Secrets

We can conuse them by using the Kuberntes REST API
by application that knows how to call that API directrly


# Concuming via Secrets volumes

Mounting the kuard-tls secrets volume to
/tls results in the following files:
/tls/cert.pem
/tls/key.pem



# PoD manifest


apiVersion: v1
kind: Pod
metadata:
  name: kuard-tls
spec:
  containers:
    - name: kuard-tls
    image: gcr.io/kuard-demo/kuard-amd64:1
    imagePullPolicy: Always
    volumeMounts:
    -name: tls-certs
      mountPath: "/tls"
      readOnly: true
    volumes:
      -name: tls-certs
      secret:
        secretName: kuard-tls



# Create kuard-tls POD

$ kubectl apply -f kuard-secret.yaml

$ kubectl port-forward kuard-tls 8443:8443





# Private Docker Registries

We could make our deployments via private registries
But they want credentilas

It is difficult to maintain creds for all private registries on all nodes

We could mange this by using Image pull secrets in spec in the manifest


$ kubectl create secret docker-registry my-image-pull-secret \
  --docker-username=<useraname> \
  --docker-passowrd=<passoword> \
  --docker-email=<email-address>



Enabel access by adding the following

apiVersioni: v1
kind: Pod
metadata:
  name: kuard-tls
spec:
  containers:
    - name: kuard-tls
....
  imagePullSecrets: 
  -name: my-image-pull-secret
....







### ConfigMap vs Secrets

Config map is a simple UTF-8 text 
as of kubernetes 1.6 ConfigMap are unable to store binary date


Secret data values hold data using base64 encoding.
So they could store binary data.



# Managing ConfigMap and Secrets

create
delete
get secret/configmap <name> -o yaml
describe



# UPdating

You could update a configmap or secret and have it reflecting in a running container
There is no need to restart app IF
your app is configured to reread configuration


# Update from file

IF you have manifest, you could edit and push it

kubectl replace -f <filename>

or

kubectl apply -f <filename>



# Smart Update

IF you save your ConfigMap or Secrets as separate files on disk
( as opposed to embedded into YAML directly)

You coudl use kubectl to recreate the manifest and update it:


$ kubectl create secret generic kuard-tls \
  --from-file=kuard.crt --from-file=kuard.key \
  --dry-run -o yaml | kubectl replace -f -



If we dont specify --dry-run , k8s will trough an error :
There is already such object
