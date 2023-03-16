
### Check istio settings in config map

kubectl -n istio-system get cm istio -o jsonpath="{@.data.mesh}" | grep disablePolicyChecks


# verify

kubectl get svc -n istio-system


# check istio-ingressgateway
# Notice that EXTERNAL-IP is in <pending> state, so that’s the part when MetalLB comes in.

kubectl get service -n istio-system istio-ingressgateway


# label default namespace with istio-injection
kubectl label namespace default istio-injection=enabled


# get ns with label
kubectl get namespace -L istio-injection







