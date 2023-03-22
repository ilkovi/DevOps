###  Liveness Probe - Recover from a Broken State 

apiVersion:   v1
kind:         Pod
metadata:
  name:       liveness-nginx
spec:
  containers:
  - name:     liveness
    image:    nginx
    livenessProbe:
      httpGet:
        path: /
        port: 80

