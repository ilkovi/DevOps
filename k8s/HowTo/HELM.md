


### Insalling HEML - the kubernetes package manager

Helm is the Kubernetes package manager


!! You don't write manifest files, instead you search for kubernetes packge ( chart ) from repositories and install it 

The manifests are actually templates

The values in the templates are filled when the package is instantiated by Helm

A Helm package is called a chart


Helm has a client-side CLI called helm and a server called tiller
Tiller runs within your Kubernetes cluster as a regular Kubernetes deployment





# Installation
You can build Helm from source or download it from the GitHub release page, extract the archive, and move the helm binary into your $PATH.

$ wget https://storage.googleapis.com/kubernetes-helm/ \
  helm-v2.7.2-darwin-amd64.tar.gz

$ tar -xvf helm-v2.7.2-darwin-amd64.tar.gz

$ sudo mv darwin-amd64/64 /usr/local/bin

$ helm version

$ helm init
$HELM_HOME has been configured at /Users/sebgoa/.helm.

Tiller (the helm server side component) has been installed into your Kubernetes
Cluster. Happy Helming!



OR

$ kubectl create serviceaccount --namespace kube-system tiller
$ kubectl create clusterrolebinding tiller --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
$ helm init --service-account tiller

Creating /root/.helm/repository/repositories.yaml
Adding stable repo with URL: https://kubernetes-charts.storage.googleapis.com
Adding local repo with URL: http://127.0.0.1:8879/charts
$HELM_HOME has been configured at /root/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation
Happy Helming!
ccount tiller











### Using HELM to install applications

By default, Helm comes with some chart repositories configured

These repositories are maintained by the community; you can read more about them on GitHub


1. verify that tiller is running and that you have the default repositories configured:

$ kubectl get pods --all-namespaces | grep tiller
kube-system   tiller-deploy-1491950541-4kqxx   1/1   Running   0   3m


$ helm repo list
NAME   	URL
stable 	http://storage.googleapis.com/kubernetes-charts


2. You can now search for a Application package:

$ helm search redis
NAME                    	VERSION	DESCRIPTION
stable/redis            	0.5.1  	Open source, advanced key-value store. It ...
testing/redis-cluster   	0.0.5  	Highly available Redis cluster with multiple...
...



3. Install Applicatoin

$ helm install stable/redis



Helm will create all the Kubernetes objects defined in the chart
EXAMPLE
a secret , a PVC , a service, and/or a deployment

Together, these objects make up a Helm release that you can manage as a single unit.


You now have an Application pod running

$ helm ls
NAME           REVISION	 UPDATED                   STATUS  	 CHART        ...
broken-badger  1         Fri May 12 11:50:43 2017  DEPLOYED  redis-0.5.1  ...

$ kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
broken-badger-redis-4040604371-tcn14   1/1       Running   0          3m


NOTE
You can create your own Chart of your own applications


