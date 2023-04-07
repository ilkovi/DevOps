

### example of using an Amazon EBS Volume in a pod.


Volumes could be:
- emptyDir
- hostPath
- gcePersitntDisk
- awsElasticBlockStore
    * The node on which pods are running must be an Amazon EC2 instance
    * Mounts an AWS EBS volume into a pod.
    * POD must be in the same region and availability zone as the EBS volume
    * single instance may mount an EBS volume
- nfs
- flocker
- gitRepo
- persistentVolumeCalim
- azureFileVolume

  

  
  
# Setting the Environment

1. SSH log in to the EC2 instance using the public IP address

2. Spin up a CloudFormation consisting of a single controller and three worker nodes

3. Obtain the Kubernetes clusters’ controller’s public IP and add an A record for it to the public DNS

4. SSH log in in to the controller instance using the public IP:
$ ssh -i "kubernetes-coreos.pem" core@52.1.116.171

5. Install kubectl binaries and list the nodes in the cluster:
$ ./kubectl get nodes












# Creating a AWS Volume

1. Click on Create Volume in the EC2 Console

NOTE
The EBS volume must be created in the same availability zone 
as the EC2 instance on which a pod is to mount the EBS volume

2. In the Create Volume dialog, set
- Volume Type as General Purpose SSD (GP2)
- Size as 100 GiB
- Availability Zone as us-east-1c

Click Create.

# OR

aws ec2 create-volume command

$ aws ec2 create-volume --availability-zone us-east-1c --size 10 --volume-type gp2














# Using a awsElasticBlockStore Volume

we will use the EBS volume in a ReplicationController specification file

Specify the fsType as ext4 and the volume name as aws-volume:

apiVersion: v1
kind: ReplicationController
..
volumeMounts:
-
mountPath: /aws-ebs
name: aws-volume
volumes:
-
awsElasticBlockStore:
fsType: ext4
volumeID: "aws://us-east-1c/vol-62a59dc8"
name: aws-volume
















#  Creating a Git Repo

1. Crate a Git Repo

2. Optionally add pod.yaml in it

3. Obtain the HTTPS web URL for the Git repo






# Using a gitRepo Volume


1. vi pod.yaml

volumes:
-
gitRepo:
repository: ""
revision: ""
name: git-volume


