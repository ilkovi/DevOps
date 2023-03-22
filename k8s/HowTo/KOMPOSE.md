
####  KOMPOSE 

Converting Your Docker Compose Files to Kubernetes Manifests



# Install Kompose
To start, download kompose from the GitHub release page and move it to your $PATH,

$ wget https://github.com/kubernetes-incubator/kompose/releases/download/ \
       v1.6.0/kompose-darwin-amd64

$ sudo mv kompose-darwin-amd64 /usr/local/bin/kompose

$ sudo chmod +x /usr/local/bin/kompose

$ kompose version
1.6.0 (ae4ef9e)




# Convert to stdout
convert this into Kubernetes manifests with the following command

$ kompose convert --stdout

The manifests will be printed to stdout and you will see a Kubernetes service and a deployment
 as a result. 

To create these objects automatically, you can use the Docker compose-compliant command up like so:

$ kompose up



NOTE
By default, kompose converts your Docker services into a Kubernetes deployment and associated service. You can also specify the use of a DaemonSet (see Recipe 7.3), or you can use OpenShift-specific objects such as a DeploymentConfiguration.

WARNING
While in general it doesn’t cause problems, it is possible that the conversion may not result in a working manifest in Kubernetes. This is expected as this type of transformation cannot be perfect. However, it gets you close to a working Kubernetes manifest