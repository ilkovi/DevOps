

#### Install MetalLB		latest version Jul 2019 - v0.8.1



kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.8.1/manifests/metallb.yaml

# Check
kubectl get pods -n metallb-system


# Apply ConfigMap

$  vi metal.yaml

apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: custom-ip-space
      protocol: layer2
      addresses:
      - 192.168.99.100/28



###### 172.172.122.112/29 === 113-118 usable  + 112 network id + 119 broadcast 

kubectl apply -f metal.yaml
