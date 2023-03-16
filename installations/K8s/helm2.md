
#### download helm


 Download from :
 
 https://github.com/helm/helm/releases

 https://get.helm.sh/helm-v2.14.3-linux-amd64.tar.gz


 tar -zxvf helm-v2.0.0-linux-amd64.tgz
 
 mv linux-amd64/helm /usr/local/bin/helm
 
 

 
 # Configure
 
 kubectl create serviceaccount --namespace kube-system tiller
 
 kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
 
 kubectl --namespace=kube-system create clusterrolebinding add-on-cluster-admin --clusterrole=cluster-admin --serviceaccount=kube-system:default


 
 # init 

 helm init --service-account tiller --history-max 200

 
 

  # install heapster
 
 helm install --name heapster stable/heapster --namespace kube-system
 

 # isntall dashboard
 
 helm install stable/kubernetes-dashboard --name dashboard --namespace kube-system
 
 
  
 

 
 # delete the dashboard
 
 helm delete my-release
 
 # delete all
 
 helm del --purge istio

 
 
 # list 
 
 helm list
 
 # list repo
 
 helm repo list
 




#### fixing helm - for version 1.16

helm init --history-max 200 --output yaml > tiller.yaml


    change to apps/v1
    add the selector field

  selector:
    matchLabels:
      app: helm
      name: tiller
