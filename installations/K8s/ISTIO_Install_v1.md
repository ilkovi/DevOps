
https://www.digitalocean.com/community/tutorials/how-to-install-and-use-istio-with-kubernetes


helm repo add istio.io https://storage.googleapis.com/istio-release/releases/1.1.7/charts/

helm repo list

helm install --name istio-init --namespace istio-system istio.io/istio-init

# Check
kubectl get crds | grep 'istio.io\|certmanager.k8s.io' | wc -l

-- there should be 53 


# isntall it	 --- Working		
helm install --name istio --namespace istio-system --set grafana.enabled=true istio.io/istio

				(--set kiali.enabled=true # optional web interface to show mesh)
				--set global.controlPlaneSecurityEnabled=true
				--set mixer.adapters.useAdapterCRDs=false
				--set grafana.enabled=true 
				--set grafana.security.enabled=true
				--set tracing.enabled=true
				


### Additional Options

helm install istio.io/istio \
    --name istio \
    --namespace istio-system \
    --set global.controlPlaneSecurityEnabled=true \
    --set global.mtls.enabled=true \
    --set grafana.enabled=true \
    --set servicegraph.enabled=true \
    --set tracing.enabled=true \
	--set kiali.enabled=true



#OPTIONAL 1

 helm upgrade --recreate-pods --namespace istio-system --set grafana.enabled=true istio install/kubernetes/helm/istio


# OPTIONAL 2

helm upgrade --recreate-pods --namespace istio-system --set grafana.enabled=true \
             --set global.configValidation=false \
             --set sidecarInjectorWebhook.enabled=false \
             istio \
             install/kubernetes/helm/istio
