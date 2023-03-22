

Tectonic is a Kubernetes platform providing enterprise-level 
security
scalability,
and reliability.


Tectonic Console is a graphical user interface (GUI) to manage a Kubernetes cluster from a web browser. The
Console may be used to deploy new applications, create rolling upgrades for deployments, and create pods,
replication controllers, and services. Some of the benefits of Tectonic Console are as follows:
–– Out-of-the-box Kubernetes cluster
–– Authorization framework
–– Enterprise authentication
–– Improved scalability
–– User-friendly Dashboard
–– Scheduled Updates for cluster software
–– Flexible architectures
–– Automatic Load Balancing and Services
–– Rollbacks
–– Better machine utilization
–– Environment consistency across teams
–– Built-in credentials storage and distribution
–– Demarcation between OS and applications
–– LDAP-based secure authentication




Tectonic provides the following services for applications deployed in a users cluster:
Tectonic Console    - "Tectonic Console is a web management console for a Kubernetes cluster"
Tectonic Identity   - "management for services on a Kubernetes cluster" 
Tectonic Support    - "support from a team."
Tectonic Wizard     - "installation and configuration wizard"  
Tectonic Manager    - "management of the Tectonic services themselves"





Tectonic basic      - free
Tectonic lab        - fee based
Tectonic enterprise - fee based



### Accessing the Tectonic Console
The following command sets up port forwarding from 127.0.0.1:9000 to port 9000 on the pod labelled
app=tectonic-console: (from the master)

kubectl get pods -l tectonic-app=console -o template --template="{{range.items}}{{.metadata.
name}}{{end}}" | xargs -i{} kubectl port-forward {} 9000



# AWS port forward
To invoke the Tectonic Console in a web browser we still need to set another port forwarding from a
local machine to the controller machine, which has public IP 23.20.52.23 and public DNS ec2-23-20-52-23.
compute-1.amazonaws.com. A port other than 9000 could be used on the local machine to forward to the
Tectonic Console port. The command looks like this:


ssh -i kubernetes-coreos.pem -f -nNT -L 9001:127.0.0.1:9000 core@ec2-23-20-52-23.compute-1.
amazonaws.com
