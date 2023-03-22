###  Setting Quotas Within a Namespace

Use a ResourceQuota object 

$ cat resource-quota-pods.yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: podquota
spec:
  hard:
    pods: "10"

NOTE
You can set a number of quotas on a per-namespace basis, 
including but not limited to pods, secrets, and config maps.