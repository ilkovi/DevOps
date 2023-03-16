1. Download sonar

wget -O /etc/yum.repos.d/sonar.repo http://downloads.sourceforge.net/project/sonar-pkg/rpm/sonar.repo

yum install sonar

vi /opt/sonar/conf/sonar.properties
uncomment the MySQL line
sonar.jdbc.username=sonar
sonar.jdbc.password=sonar


2. Install MySQL

wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm
rpm -ivh mysql-community-release-el7-5.noarch.rpm

yum install mysql-server
systemctl start mysqld

# Harden MySQL Server
mysql_secure_installation
-- password

# Root Login
mysql -u root -p


3. Add database

CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci;
CREATE USER 'sonar' IDENTIFIED BY 'sonar';
GRANT ALL ON sonar.* TO 'sonar'@'%' IDENTIFIED BY 'sonar';
GRANT ALL ON sonar.* TO 'sonar'@'localhost' IDENTIFIED BY 'sonar';
FLUSH PRIVILEGES;


4. Start Sonar

service sonar start

5. Enable firewall

firewall-cmd --zone=public --add-port=9000/tcp --permanent
firewall-cmd --reload


6. Access the web

http://10.230.29.78:9000/
admin / admin


7. Install Sonar Runner

cd /opt
 wget http://repo1.maven.org/maven2/org/codehaus/sonar/runner/sonar-runner-dist/2.2.2/sonar-runner-dist-2.2.2.zip
 unzip sonar-runner-dist-2.2.2.zip
 ln -s sonar-runner-2.2.2 sonar-runner

# edit
 
vi /opt/sonar-runner/conf/sonar-runner.properties
 
sonar.host.url=http://10.230.16.221:9000
sonar.login=jenkins
sonar.password=jenkins
export SONAR_SCANNER_OPTS="-Xmx512m"

 
# add env variable

# cat /etc/profile.d/sonar-runner.sh
#!/bin/bash
export SONAR_RUNNER_HOME=/opt/sonar-runner
export PATH=$PATH:$SONAR_RUNNER_HOME/bin


# verify

sonar-scanner -v
Sonar Runner 2.2.2
Java 1.8.0_111 Oracle Corporation (64-bit)
Linux 3.10.0-327.el7.x86_64 amd64


INFO:
http://cvuorinen.net/2013/07/installing-sonarqube-with-jenkins-integration-for-a-php-project/
 

Other STAFF: ( ESB_CODE_TEST)

echo -e "sonar.projectVersion=${DATE} \n \
sonar.projectName=Sonar_Continous_ESB \n \
sonar.sources=src/main/java/hello/ \n \
sonar.projectKey=esb_scan \n \
sonar.sourceEncoding=UTF-8 \n \
\n \
sonar.modules=module1, module2 \n \
 \n \
module1.sonar.projectName=Sonar_Continous_ES_Module_1 \n \
module1.sonar.language=java \n \
module1.sonar.sources=Optima/main/bl_framework_src \n \
#module1.sonar.binaries=target/classes \n \
module1.sonar.projectBaseDir=. \n \
 \n \
module2.sonar.projectName=Sonar_Continous_ES_Module_2 \n \
module2.sonar.language=java \n \
module2.sonar.sources=Optima/main/optima_bl_src \n \
#module2.sonar.binaries=target/classes \n \
module2.sonar.projectBaseDir=. " > sonar-project.properties