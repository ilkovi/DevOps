1. Install
yum install java-1.8.0-openjdk.x86_64

rpm -ivh jenkins-2.7.4-1.1.noarch.rpm
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --reload
sudo -u jenkins ssh-keygen

# Check
firewall-cmd --list-all


2. Start / Stop jenkins

service jenkins start/stop/restart
chkconfig jenkins on
