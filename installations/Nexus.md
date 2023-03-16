Prereq: install java

1. Download nexus : https://www.sonatype.com/download-oss-sonatype

~# cat .wgetrc
use_proxy=yes
http_proxy=http://10.232.233.70:8080
~# cat .curlrc
proxy = proxy=http://10.232.233.70:8080 # create user nexus

useradd -m -d /home/nexus -s /bin/bash -c "nexus repository" -u 1001 -U nexus

‘-U‘ argument create/adds a group with the same name as the user.

2. Move it under [ /opt/local ]

# cp nexus-2.13.0-01-bundle.tar.gz /usr/local
# cd /usr/local
# tar xvzf nexus-2.13.0-01-bundle.tar.gz
# ln -s nexus-2.13.0-01 nexus

3. Export home

### in order to remove old link please use # unlink link_name

cd /etc/profile.d
# cat nexus.sh
NEXUS_HOME=/usr/local/nexus
export NEXUS_HOME

4. switch to nexus user and edin conf file
su - nexus
vi /usr/local/nexus/bin/nexus
NEXUS_HOME="/usr/local/nexus"
RUN_AS_USER=nexus

vi ~/.bash_profile
NEXUS_HOME=/usr/local/nexus
export NEXUS_HOME
PATH=$PATH:$HOME/.local/bin:$HOME/bin:$NEXUS_HOME/bin
export PATH

5. start nexus
$ nexus start

5. Add exception in the firewall

firewall-cmd --zone=public --add-port=8081/tcp --permanent
firewall-cmd --reload

### Configure Nexus 2 and 3 documentation
http://books.sonatype.com/nexus-book/2.13/reference/install-sect-service.html

### Integration with Jenkins
https://support.sonatype.com/hc/en-us/articles/227256688-How-do-I-configure-the-Nexus-Jenkins-Plugin

### enable this plugin "Nexus Jenkins Plugin" in order to crete task for upload to jenkins