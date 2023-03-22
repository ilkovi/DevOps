
#### Automatically Resizing a Cluster in AWS

You want your Kubernetes cluster running in AWS EC2 to automatically grow or 
shrink in terms of the number of nodes, depending on the utilization.


Use the AWS Cluster Autoscaler, a Helm package leveraging AWS autoscaling groups. 
