

#### Config MAP

# Conigmaps are used to provide configuration information for workloads
- a short string
- comprihensive value in the form of a string




### CONFIG MAP
think of it as a small filesytem
- environment variable 
- command lines for your containers
- it is conmbined witht the POD right before run



# Create a Config MAP

create a file my-config.txt
# This is a config file for containers
parameter1 = value1
parameter2 = value2

$ kubectl create configmap my-config \
 --from-file=my-config.txt \
 --from-literal=extra-param=extra-value \
 --from-literal=another-param=another-value



# view the config map intormation

$ kubectl get configmaps my-config -o yaml

selfLink: /api/v1/namespaces/default/configmaps/my-config




NOTE: As you could see, the config map is a key/value pair stored in a object






!! IMPORTANT: 
There are three ways to use ConfigMap

 1. Filesystem
	you can mount the ConfigMap into a POD

 2. Environment variable
	ConfigMap can be used to set value of env

 3. Command-line argument
	Dynamically creating the command line for a container
	based on ConfigMap values





apiVersion: v1
kind: Pod
metadata:
  name: kuard-config
spec:
  containers:
    - name: test-container
      image: gcr.io/kuar-demo/kuard-amd64:1
      imagePullPolicy: Always
      command:
	- "/kuard"
	- "$(EXTRA_PARAM)"
      env:
	- name: ANOTHER_PARAM
	  valueFrom:
	    configMapKeyRef:
	      name: my-config
	      key: another-param
	- name: EXTRA_PARAM
	  valueFrom:
  	    configMapKeyRef:
	      name: my-config
	      key: extra-param
      volumeMounts:
	- name: config-volume
	  mountPath: /config
    volumes:
      - name: config-volume
	configMap:
	  name: my-config
    restartPolicy: Never








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
