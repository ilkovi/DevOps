
### Kubernetes on AWS

Elasticsearch 	"used to index and store logs"
Heapster        "used to analyze compute resource usage" 
Kibana          "a GUI dashboard used to view the logs" 
KubeDNS         "used to resolve DNS names for services"
Kubernetes-dashboard, Grafana, InfluxDB   "used for metrics visualization" 

AMI         "Amazon Machine Image "
VPC         "virtual private cloud"

To add all traffic between the instances Add security Group
Click on Master
Then select Actions ➤ Networking ➤ Change Security Groups


The default configuration settings used to start a new cluster are specified in the cluster/aws/configdefault.
sh
vi /home/ec2-user/kubernetes/cluster/aws/config-default.sh
export KUBE_AWS_ZONE=eu-west-1c
export NUM_NODES=3
export MASTER_SIZE=m3.medium
export NODE_SIZE=m3.medium
export AWS_S3_REGION=eu-west-1
export AWS_S3_BUCKET=mycompany-kubernetes-artifacts
export INSTANCE_PREFIX=k8s














####  Kubernetes on CoreOS on AWS

The kube-up tool does not create a production-ready cluster. One of the limitations of kube-up is that it does
not support CoreOS.

Docker is installed out-of-the-box on CoreOS. The CoreOS tool kube-aws can be used to spin up a
production-ready Kubernetes cluster on CoreOS nodes on EC2 without much configuration

AWS CloudFormation that consists of a Kubernetes Master node and three Kubernetes worker nodes, along with
an Auto Scaling Group and a Launch Configuration.

kube-aws - CloudFormation Generator
    - master (CoreOS)
    - worker nodes (CoreOS)
    - lunch configuration
    - auto scaling group

    
    
### Configuring AWS Credentials

To create new AWS Security credentials click on Security Credentials for the user account and
click on Create New Access Key to create an access key


In the Amazon Linux instance run the following command to configure the instance with the AWS credentials:

$ aws configure

Specify the access key ID and access key when prompted
Specify the default region name (us-east-1)
and the output format (json)




### Installing Kube-aws

CoreOS applications on GitHub and packaged into AppC images are signed with the CoreOS Application Signing Key

mport the CoreOS Application Signing Key, as shown here:

$ gpg2 --keyserver pgp.mit.edu --recv-key FC8A365E

Validate the key

$ gpg2 --keyserver pgp.mit.edu --recv-key FC8A365E

# Download kube-aws
Donwload the latest release tarball and detached signature (.sig) for kube-aws from https://github.
com/coreos/coreos-kubernetes/releases:


$ gpg2 --verify kube-aws-linux-amd64.tar.gz.sig kube-aws-linux-amd64.tar.gz

# Extract

$ tar zxvf kube-aws-linux-amd64.tar.gz

# Add to path

$ sudo mv linux-amd64/kube-aws /usr/local/bin










### Setting Up Cluster Parameters

• EC2 key pair

$ aws ec2 create-key-pair --key-name kubernetes-coreos --query 'KeyMaterial' --output text > kubernetes-coreos.pem

Modify te permissions

$ chmod 400 kubernetes-coreos.pem



• KMS key

KMS key, which is used to encrypt and decrypt cluster TLS assets and is identified by an
Amazon Resource Name (ARN) string.

$ aws kms --region=us-east-1 create-key --description="kube-aws assets"

• External DNS name

Next you need to register a domain name with a domain registrar, as we shall be using the domain’s external
DNS name to make the cluster API accessible



#### Initializing the Cluster CloudFormation

Using the Amazon EC2 key pair, KMS Key ARN string, and external DNS name, initialize the CloudFormation
stack:

kube-aws init --cluster-name=kube-coreos-cluster
--external-dns-name=NOSQLSEARCH.COM
--region=us-east-1
--availability-zone=us-east-1c
--key-name=kubernetes-coreos
--kms-key-arn="arn:aws:kms:us-east-1:672593526685:key/b7209ba2-cb87-4ccf-8401-5c6fd4fb9f9b "


### Summary
In this chapter we launched an AWS CloudFormation stack for a Kubernetes cluster on CoreOS instances.
The procedure we followed was this: First, install kube-aws. Next, set up the cluster parameters, such as
creating a KMS key and setting up an external DNS name. To create the cluster CloudFormation, create an
asset directory, initialize the cluster CloudFormation, render contents of the asset directory, customize the
cluster, validate the cluster and launch the cluster.




