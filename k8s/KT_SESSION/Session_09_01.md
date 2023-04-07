


### check logs of a container



docker logs -t -f --tail 10 node

- t -show timestamp
- f -dinamically
--tail 10 -show last number of logs
node -is the name of the container


### attach to container

docker exec -it node sh

*log are written into the container
*you could mount the log folder to outside 


docker exec -it node ps
- will list the processes inside, and not enter into the container


### How to save docker into file ( question from the certification )

docker save --output somefile.tar ..image.name..

docker load < somefile.tar


### Limit resources

docker run -p 3000:3000 -name node-v1 --cpu=1 --restart=always --hostname demo helloworld:v0.0.1

( use 1 cpu )
( if creash, restart the docker )
( set hostname demo inside the container )




!! Important 

Two types of applications for container

-stateFULL
` depend on data mounted on the storage ( DB, WebServer(writes into the strorage))
`
` multiple app could be run, with a shared storages
` or one app writes, and multiple are reading

-stateLESS
` do not depend on storage
`- they could be scaled up to ~
`- you could have application, that read/writes to/from the memory
` ( then start a second replica of the app - IT doesN'T know about the data in the memory)
`
` In this case use MEM CASH - like Redis or KAFKA ( topics )
`
` when app run, it is configured to load its data ( topics ) in Redis or Kafka
`
` the following apps that run, they read from the MEM CACHE








!! Important 


GCP Anthos - build 2019
Include Google OpenSource Istio and Knative 
Based on GKE, Anthos takes care of any K8s updates and security patches automatically
as they are released.
- in the cloud
- on prem - bare metal or VMWare ( partner with VMware, Dell, HPE and Lenovo )


*Components
Anthos combines the Google Cloud managed service 
- Google Kubernetes Engine (GKE), 
- GKE On-Prem,
- and the Anthos Config Management console for unified administration,
 policies, and security across hybrid and multicloud Kubernetes deployments.





*Competitors
AWS hybird cloud front  - AWS Outposts
( extension of AWS cloud to on-premises data centers )
AWS configured HW and AWS managed services and APIs

Oracle cloud , MS Azure Stack
simalar solutions





### GCP Cloud Run ( remove K8s support from customer )
Anthos + GKE supported by GCP

You could run hybrid, 
run Cloud Run + Anthos on your HW, but then you support the K8s cluster ??









### Note

sometimes , containers run in different Time Zone

solution

Mount host Time Zone files into the docker






### Note - docker logs in the cloud

if you not mount docker logs with volume from the host

You could set up Log Stash in image
 - sends logs to ElasticSearch and combine  (ElasticSearch is expensive - TB of logs)
 - make reqular expressions and search for your logs





