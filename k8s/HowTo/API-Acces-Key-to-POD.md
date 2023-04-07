
### Passing an API Access Key to a Pod Using a Secret

Problem
As an admin, you want to provide your developers with an API access key in a secure way;
 that is, without sharing it in clear text in your Kubernetes manifests.

1. First, create a text file called passphrase that holds the passphrase

$ echo -n "open sesame" > ./passphrase


2. create the secret, using the passphrase file:

kubectl create secret generic pp --from-file=./passphrase


From an admin point of view, you’re all set now and it’s time for your 
developers to consume the secret

You would consume the secret, for example, by mounting it as a volume
 into your pod and then reading it out as a normal file


apiVersion:        v1
kind:              Pod
metadata:
  name:            ppconsumer
spec:
  containers:
  - name:          shell
    image:         busybox
    command:
      - "sh"
      - "-c"
      - "mount | grep access  && sleep 3600"
    volumeMounts:
      - name:      passphrase
        mountPath: "/tmp/access"
        readOnly:  true
  volumes:
  - name:          passphrase
    secret:
      secretName:  pp


NOTE
Secrets exist in the context of a namespace, so you need to take that into account when setting them up
the size of a secret is limited to 1 MB.

