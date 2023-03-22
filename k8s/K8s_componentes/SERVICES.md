


# Importing an external service

kind: Service
metadata:
  name: my-database
  # note 'test' namespace here
  namespace: test
...


IN PROD
kind: Service
metadata:
  name: my-database
  # note 'prod' namespace here
  namespace: prod
...


When you deploy pod in the test namespace, it resolves database to :
mydatabase.test.svc.cluster.internal
In Prod the contrast is clear:
my-database.prod.svc.cluster.internal









( External database wiht kubernetes ) HOSTNAME FOR THE database

# Service without a selector


kind: Service
apVersion: v1
metadata:
  name: exteranl-database
spec:
  type: ExteranlName
  exteranalName: "database.company.com"


TYPICALLY:
kubectl create service =
1. Assign Virtual IP address 
2. kubernetes DNS populate A record for this IP address


SERVICE with EXTERNAL NAME:
kubernetes DNS add CNAME(alias) that points to your specific IP/Hostanem
in our case database.company.com
When
Applicaitoin in the cluster make a DNS lookup for
external-database.svc.default.cluster 
the DNS protocol alias the to
database.company.com











( External database wiht kubernetes ) IP FOR THE database

1. Create service without label selector/ExternalName

kind: Service
apiVersion: v1
metadata:
  name: external-ip-database



At this point:
Kubernetes will allocate a virtual IP address. Add A record in DNS

Because:
there is no label selector, there will be NO ENDPOINTS
for load balancer to redirect traffic to



Create ENDPOINTS manaually:

kind: Endpoints
apiVersion: v1
metadata:
  name: exteranal-ip-database
subsets:
  - addresses:
    - ip: 192.168.0.1
    ports:
    - port: 3306
 


!! Once Endpoints are created, the loadbalancer will start redirecting traffic
!! from Kubernetes service to IP address endpoints



