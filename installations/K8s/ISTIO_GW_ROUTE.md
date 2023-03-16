

### this is the file

apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: istio-gateway
spec:
  selector:
    istio: ingressgateway #default istio ingressgateway
  servers:
  - port:
      number: 80
      name: http-istio-gateway
      protocol: HTTP
    hosts:
    - "*"
    tls:
      httpsRedirect: true
  - port:
      number: 443
      name: https-istio-gateway
      protocol: HTTP
    hosts:
    - "*"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: api-gateway
spec:
  gateways:
  - istio-gateway
  hosts:
  - "*"
  http:
  - match:
    - uri:
        prefix: /socket.io/
    route:
    - destination:
        host: api-gateway-ws.default.svc.cluster.local
        port:
          number: 5001
  - match:
    - uri:
        prefix: /
    route:
    - destination:
        host: api-gateway.default.svc.cluster.local
        port:
          number: 5000
		  
		  
		  
##### This is the fix

apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: api-gateway
spec:
  gateways:
  - istio-gateway
  hosts:
  - "*"
  http:
  - match:
    - uri:
        prefix: "/socket.io"
    route:
    - destination:
        host: api-gateway-ws.default.svc.cluster.local
        port:
          number: 5001
    websocketUpgrade: true
  - route:
    - destination:
        host: api-gateway.default.svc.cluster.local
        port:
          number: 5000

