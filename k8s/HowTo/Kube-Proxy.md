

### ACCESS POD or Service 

** with port-forward

# Simple

$ kubectl port-forward nginx 80:8080

open in the brower http://<kube_master_ip>:80


# Advance

export POD_NAME=$(kubectl get pods -n kube-system -l "app=kubernetes-dashboard,release=dashboard" -o jsonpath="{.items[0].metadata.name}")
 
* if not specify --address , it is only to 127.0.0.1
 
kubectl -n kube-system port-forward $POD_NAME 8443:8443 --address 0.0.0.0



** with proxy

# Access API service

$ kubectl proxy --port=8001 --api-prefix=/

open browser http://<kube_master_ip>:8001/

NOTE
  --port and --api-prefix are optional.

API structure:

•/api/v1
•/apis/apps
•/apis/authentication.k8s.io
•/apis/authorization.k8s.io
•/apis/autoscaling
•/apis/batch




For Example
Pods, services, config maps, and secrets are all part of the /api/v1 API group
Deployments are part of the /apis/extensions/v1beta1 API group.





!! All API resources are either objects or lists
All resources have a kind and an apiVersion. 
In addition, every object kind must have metadata.
 The metadata contains the name of the object, the namespace , and potentially some labels and annotations 





