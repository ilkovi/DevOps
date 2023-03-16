
#### KUBECTL commands


kubectl get all --all-namespaces   

kubectl get svc --all-namespaces -o wide

kubectl -n kube-system describe pod/kubernetes-dashboard

kubectl edit svc <service_name> -n <namespace>

kubectl get pods --show-labels


kubectl cordon/uncordon <node>		# disableSchedule on node



# ssh to pod

 kubectl -n istio-system exec -it pod/istio-pilot-5896b77779-zn262 -- /bin/bash
 
 
### restart pod
kubectl replace --force -f <resource-file>


### Get NODES ID and CIDR
kubectl get nodes -o json | jq '.items[] | .spec'

kubectl get nodes -o json | jq '.items[] | .status | .addresses'

kubectl get events --sort-by=.metadata.creationTimestamp





### kube-proxy

 export POD_NAME=$(kubectl get pods -n kube-system -l "app=kubernetes-dashboard,release=dashboard" -o jsonpath="{.items[0].metadata.name}")
 
 # if not specify --address , it is only to 127.0.0.1
 
 kubectl -n kube-system port-forward $POD_NAME 8443:8443 --address 0.0.0.0





#### Print KUBELET configuration with KUBEADMIN

kubeadm config print init-defaults --component-configs KubeletConfiguration

