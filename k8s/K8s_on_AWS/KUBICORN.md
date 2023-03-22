#### KUBICORN

create a Kubernetes cluster on AWS

Since kubicorn currently doesn’t provide for binary releases, you need to have Go installed for the following to work.

$ go version
go version go1.8 linux/amd64

$ yum group install "Development Tools" \
  yum install ncurses-devel

$ go get github.com/kris-nova/kubicorn


To continue, you need to have an AWS Identity and Access Management (IAM)
 user with the following permissions available: 
AmazonEC2FullAccess, 
AutoScalingFullAccess, and 
AmazonVPCFullAccess