
### Donwload HELM

zypper search helm / zypper install helm
yum search helm / yum install helm
wget ....




### SETUP

"setup rbac"
vi rbac-config.yaml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
    
    
$ kubectl create -f rbac-config.yaml


### Work
helm list
helm repo update
- ingress-nginx
- istio
- apache
- grafana
- kuberntes-dashboard
- stable
- bitnami
- taefik
helm search repo traefik
helm install traefik traefik/traefik 
helm list






### Install Triller ( the server part )

helm init --service-account tiller






### List
helm ls


### Delete DNS
$ helm del --purge coredns







### Install Dashboard


helm install --name heapster-default \
--namespace=kube-system stable/heapster \
--version=0.2.7 --set rbac.create=true


helm install --namespace=kube-system \
--name=kubernetes-dashboard stable/kubernetes-dashboard \
--version=0.6.1

helm upgrade kubernetes-dashboard stable/kubernetes-dashboard --set \
service.type=NodePort





