


https://knative.dev/docs/install/installing-istio/#installing-istio-with-sds-to-secure-the-ingress-gateway

## download istio
 curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.3.1 sh -




## install crd

 # Download and unpack Istio
   export ISTIO_VERSION=1.3.1
   curl -L https://git.io/getLatestIstio | sh -

   
cd istio-${ISTIO_VERSION}
   
for i in install/kubernetes/helm/istio-init/files/crd*yaml; do kubectl apply -f $i; done

# crate istio ns


apiVersion: v1
kind: Namespace
metadata:
  name: istio-system
  labels:
    istio-injection: disabled

kubectl apply -f   
   
   
# create template


helm template --namespace=istio-system \
  --set sidecarInjectorWebhook.enabled=true \
  --set sidecarInjectorWebhook.enableNamespacesByDefault=false \
  --set global.proxy.autoInject=enabled \
  --set global.disablePolicyChecks=false \
  --set prometheus.enabled=true \
  --set mixer.adapters.prometheus.enabled=true \
  --set global.disablePolicyChecks=false \
  --set gateways.istio-ingressgateway.autoscaleMin=1 \
  --set gateways.istio-ingressgateway.autoscaleMax=1 \
  --set gateways.istio-ingressgateway.resources.requests.cpu=500m \
  --set gateways.istio-ingressgateway.resources.requests.memory=256Mi \
  --set gateways.istio-ingressgateway.sds.enabled=true \
  --set pilot.autoscaleMin=2 \
  --set pilot.traceSampling=100 \
  --set kiali.enabled=true \
  --set grafana.enabled=true \
  install/kubernetes/helm/istio > ./istio.yaml
  
  
kubectl apply -f istio.yaml


### Check  istio-ingressgateway service again

kubectl get service -n istio-system istio-ingressgateway
