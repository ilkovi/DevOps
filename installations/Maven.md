1. Installation


wget https://repo.maven.apache.org/maven2/org/apache/maven/apache-maven/3.5.4/

tar xvf apache-maven-3.5.4-bin.tar.gz

mv apache-maven-3.5.4 /opt
export JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk-1.8.0.65-3.b17.el7.x86_64
export MAVEN_HOME=/opt/apache-maven-3.2.1
export PATH=$PATH:$MAVEN_HOME/bin


### Verify

mvn -version

### Add proxy

In the installation dir : /opt/maven/conf/    settings.xml
    <proxy>
      <id>optional</id>
      <active>true</active>
      <protocol>http</protocol>
      <username></username>
      <password></password>
      <host>genproxy.amdocs.com</host>
      <port>8080</port>
      <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
