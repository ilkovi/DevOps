


### AWS Zones/ Region

Amazon AWS availability zones are distinct physical locations 
- with independent power, 
- network and security and insulated from failures in other availability zones. 

Availability zones within the same region have low latency network connectivity 
between them.


Starting with Kubernetes 1.2, a cluster may be provisioned across multiple cloud provider zones.
The pods managed by a replication controller are spread across zones
failure of a single zone does not affect the availability of the replication controller or service in other zones


Zones are supported only with the GCE (Google Compute Engine) and AWS (Amazon Web Services)cloud providers. 
AWS refers to the zones as “availability zones.”


NOTE
Pods that specify a persistent volume are
placed in the same zone as the volume.







### Configuring cluster.yaml for Multiple Zones/

By default a single zone is used to launch a CloudFormation. Next, we shall customize the CloudFormation
to configure multiple zones. Open the cluster.yaml file in a vi editor:

$ sudo vi cluster.yaml

- comment out the availabilityZone
- Set workerCount to 6 .the worker nodes must be set to at least the number of zones to configure. 
- instanceCIDR must be commented out
- In setting subnets, specify the
  Kubernetes subnets and their CIDRs and availability zones.
  ( At least two subnets must be specified for high availability of zones )



# check the availability zones
ec2-availability-zones –aws-access-key <access key id> --aws-secret-key <access key>

# The instanceCIDR block specifies the range of IPs to be used. Range ( between /16 and /28  netmask )

subnets:
-
availabilityZone: us-east-1b
instanceCIDR: "10.0.0.0/24"
-
availabilityZone: us-east-1c
instanceCIDR: "10.0.1.0/24"
-
availabilityZone: us-east-1d
instanceCIDR: "10.0.2.0/24"


- commented out is controllerIP
  With two or more subnets the controller is placed in the first subnet,
  controllerIP must be included in the instanceCIDR of the first subnet
  



#### Launching the CloudFormation

After we modify cluster.yaml, the CloudFormation stack must be validated.

$ kube-aws validate

Launch the CloudFormation stack.

$ kube-aws up











#### Using Multiple Zones on AWS



IMPORTANT
If a Kubernetes cluster is to be started with multi-zone capability,
 the MULTIZONE parameter must be set to true. 
 
Setting MULTIZONE to true does not automatically start nodes running in multiple zones
it only adds the capability to manage a multi-zone cluster.




1. Run the following command to start a cluster
in zone us-east-1c with three nodes:


curl -sS https://get.k8s.io | MULTIZONE=true KUBERNETES_PROVIDER=aws KUBE_AWS_ZONE=us-east-1c NUM_NODES=3 bash

*The NUM_NODES value sets the number of nodes to create


# List nodes

$ kubectl get nodes --show-labels

The labels include 
failure-domain.beta.kubernetes.io/region for the region and 
failure-domain.beta.kubernetes.io/zone for the zone.



Because the setting makes the cluster multi-zone
aware and not multi-zone to start with.
 We shall discuss subsequently adding node sets in other zones using
the same master controller.



# Next, start another node set in a different zones

KUBE_USE_EXISTING_MASTER=true MULTIZONE=true KUBERNETES_PROVIDER=aws KUBE_AWS_ZONE=us-east-
1b NUM_NODES=3 KUBE_SUBNET_CIDR=172.20.1.0/24 MASTER_INTERNAL_IP=172.20.0.9 kubernetes/
cluster/kube-up.sh


NOTE
The EC2 console lists another set of nodes in a different zone,




# Launch another node set in the us-east-1d zone using the same master node

KUBE_USE_EXISTING_MASTER=true MULTIZONE=true KUBERNETES_PROVIDER=aws KUBE_AWS_ZONE=us-east-
1d NUM_NODES=3 KUBE_SUBNET_CIDR=172.20.2.0/24 MASTER_INTERNAL_IP=172.20.0.9 kubernetes/
cluster/kube-up.sh





IMPORTANT
PVC and respective PV are located in one zone
They are not cross zone configurable.

If you crate an POD(application) using a PV
The POD will be placed in the same zone as the PVC was created !!

