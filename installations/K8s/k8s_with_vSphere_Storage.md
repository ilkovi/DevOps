
https://vmware.github.io/vsphere-storage-for-kubernetes/documentation/existing.html




### On power off machines

disk.enableUUID to 1







### Set storage class as default

 kubectl patch storageclass <your-class-name> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

kubectl patch storageclass fast -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'


# edit Storige class
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
	


#### /etc/kubernetes/vsphere.conf



[Global]
user = "administrator@vst.lab"
password = "Password1!"
port = "443"
insecure-flag = "1"

[VirtualCenter "172.17.12.70"]
datacenters = "LAB"

[Workspace]
server = "172.17.12.70"
datacenter = "LAB"
default-datastore = "lab-ext-vc11-01"
resourcepool-path = "cluster-xstream/Resources"
folder = "kubernetes"

[Disk]
scsicontrollertype = pvscsi







#### Add Cloud Provider to kubelet config

vi /var/lib/kubelet/kubeadm-flags.env

ADD KUBELET_KUBEADM_ARGS=" ...--cloud-provider=vsphere --cloud-config=/etc/kubernetes/vsphere.conf"

systemctl daemon-reload && systemctl restart kubelet





#### Edit kubeapi-server

vi /etc/kubernetes/manifests/kube-apiserver.yaml
...
spec:
  containers:
  - command:
    - kube-apiserver
    - --advertise-address=172.17.12.124
....
    - --cloud-provider=vsphere
    - --cloud-config=/etc/kubernetes/vsphere.conf

!! 

under volumesMounts:
    - mountPath: /etc/kubernetes/vsphere.conf
      name: vsphere-volume
      readOnly: true

!! 

under volumes:

  - hostPath:
      path: /etc/kubernetes/vsphere.conf
    name: vsphere-volume






#### Edit Controller Manager

vi /etc/kubernetes/manifests/kube-controller-manager.yaml
...
spec:
  containers:
  - command:
    - kube-controller-manager
...
    - --cloud-provider=vsphere
    - --cloud-config=/etc/kubernetes/vsphere.conf
    image: k8s.gcr.io/kube-controller-manager:v1.15.4


!! under volumesMounts:


    - mountPath: /etc/kubernetes/vsphere.conf
      name: vsphere-volume
      readOnly: true

!!  under volumes:


  - hostPath:
      path: /etc/kubernetes/vsphere.conf
    name: vsphere-volume






#### ON all NODEs
# add "--cloud-provider=vsphere" on all worker nodes for kubelet

vi /var/lib/kubelet/kubeadm-flags.env
--cloud-provider=vsphere

systemctl daemon-reload && systemctl restart kubelet








### In oreder vsphere to recongnize VM by id, the following is to verify


https://github.com/kubernetes/kubernetes/issues/65933


## Verify
kubectl get nodes -o json | jq '.items[]|[.metadata.name, .spec.providerID]'

## Get UUID
kubectl get nodes -o json | jq '.items[]|[.metadata.name, .spec.providerID, .status.nodeInfo.systemUUID]'

## Add UUID
kubectl patch node <Node name> -p '{"spec":{"providerID":"vsphere://<vm uuid>"}}'



additional check

cat /sys/class/dmi/id/product_serial | sed -e 's/^VMware-//' -e 's/-/ /' | awk '{ print toupper($1$2$3$4 "-" $5$6 "-" $7$8 "-" $9$10 "-" $11$12$13$14$15$16) }'

to match 
cat /sys/class/dmi/id/product_uuid




kubectl patch node master -p '{"spec":{"providerID":"vsphere://420307EB-56CB-B3CB-3867-9E72FE706867"}}'
kubectl patch node worker1 -p '{"spec":{"providerID":"vsphere://4203BA4C-494E-2DFD-2EF2-3EC4857A3E4E"}}'
kubectl patch node worker2 -p '{"spec":{"providerID":"vsphere://42034387-ED95-BB9D-023A-3E5E5D2FCAF7"}}'




### for logging add

/etc/kubernetes/manifests/kube-controller-manager.yaml

    - --v=4




















