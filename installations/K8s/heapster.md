

#### Heapster is deprecated , now it is metrics

git clone https://github.com/kubernetes-incubator/metrics-server.git

tar -zxvf metric.tar.gz

cd metrics-server/

kubectl apply -f deploy/1.8+/

kubectl get apiservices |egrep metrics







#### Install heapster

helm install --name heapster stable/heapster --namespace kube-system


#### Install heapster grafana influxdb

https://iamchuka.com/install-kubernetes-dashboard-part-iii/


