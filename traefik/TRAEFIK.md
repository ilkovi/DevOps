
https://www.youtube.com/watch?v=n5dpQLqOfqM


# Install Traefik with Helm

!! Use latest traefik repository

- in the cloud, installation with one click sometimes use old version
- there is no backward compatability for traefik between v1 and v2


# Go to artifacthub.io

- download your traefik repository

( use : ORG: Traefik Labs )

!! Customize your Traefik configuration file ( TEMPLATES )

- for example CloudDNS/DigitalOcean/else ( or your DNS provider )
otherwise traefik will try to do DNS challenge to try your domain ...



# One way of configure it
CLI commands.

put it in the additionalArguments: section of your helm chart
* trafik-values.yml
- there is documenattion with examples on the Traefik webpage


# set persistanat volume folder for the certs

- you should set an authentication 
in env: 

set key and token as secret in the same namespace as traefik




# Ports

80 - you could add permanent rediret

443 - you need to enable tls and set default cert resolver

if you set a default cert resolver ( provider )
- it will try to abtain cert for each application you are exposing in ingress


# Disable dashboard
- you don't want to expose that
- no authentication



# set persistant storage for all your certificate

!! Very important

if you restart traefik , and you don't store the certificate

traefik will need to obtain those certificates every time you restart traefik



# Change permissions of the certs in the persistant volume

How?

add a deployment , with busybox




### Ingress Class

What it is:
in k8s 1.19+ you could configure many ingress controlers
and then use anotations to link services to ingress rules

By setting the ingressClass default Class Traefik
- it will be used as default ingress controller
- save effort on putting annotations
- everytime it will provide the certs for you




