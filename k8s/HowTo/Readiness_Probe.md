
### Readiness Probe - Controlling Traffic Flow to a Pod 

apiVersion:   v1
kind:         Pod
metadata:
  name:       readiness-nginx
spec:
  containers:
  - name:     readiness
    image:    nginx
    readinessProbe:
      httpGet:
        path:  /
        port:  80



When should you use which probe? That depends on the behavior of the container, really. 
Use a liveness probe and a restartPolicy of either Always or OnFailure if your container
can and should be killed and restarted if the probe fails. If you want to send traffic
to a pod only when it’s ready, use a readiness probe. Note that in this latter case, 
the readiness probe can be the same as the liveness probe.


