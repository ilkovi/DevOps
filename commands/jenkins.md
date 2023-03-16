### GET Jenkins AIP token

http://<jenkins-server>/user/<username>/configure

....the token........


execute script

http://<username>:<api-token>@<jenkins-server>/job/<job-name>/doDelete


### Kill HANG JOB
` Go To Jenkins:8080/scripts
` edit job name / and job build number

Jenkins.instance.getItemByFullName("<__the_hang_job___>")
                .getBuildByNumber(37)
                .finish(
                        hudson.model.Result.ABORTED,
                        new java.io.IOException("Aborting build")
                );
 OR

Jenkins.instance.getItemByFullName("JOB_NAME").getBuildByNumber(NUMBER).finish(hudson.model.Result.ABORTED, new java.io.IOException("Aborting build"))



### Execute on windows node

println System.getenv("USERNAME")


println System.getenv("userprofile")

C:\Windows\system32\config\systemprofile



### RUN a Build:
curl -X POST  <URL-to-the-job>/build?delay=0sec


### Show some stuff

`go to jenkins:8080/script

com.cloudbees.plugins.credentials.SystemCredentialsProvider.getInstance().getCredentials().forEach{println it.dump().replace(' ', '\n')}

`get plugin passwords

1. get hash from the browser

println(hudson.util.Secret.decrypt("{HASHxxxxx=}"))

println(hudson.util.Secret.fromString("{AQAAABAAAAAQHU6y6yZpE0544ZI5QB+yAQ3j5XGQsBYrqjZWgQVsKys=}").getPlainText())



### Generate Cert

`Generate certificate csr
openssl req -new > new.ssl.csr

` Create a key file for generating certificate
openssl rsa -in privkey.pem -out new.cert.key

` Create a csr file using the key file for 635 days
openssl x509 -in new.ssl.csr -out new.cert.cert -req -signkey new.cert.key -days 365

` Creates intermediate pkcs12 file
openssl pkcs12 -export -out jenkins_keystore.p12 -passout 'pass:the_pass' \
-inkey new.cert.key -in new.cert.cert -name jenkins.FQDN.com

` Create Java Keystore file
    keytool -importkeystore -srckeystore jenkins_keystore.p12 \
     -srcstorepass 'the_pass' -srcstoretype PKCS12 \
     -srcalias jenkins.vst.lab -deststoretype JKS \
     -destkeystore jenkins_keystore.jks -deststorepass 'the_pass' \
     -destalias jenkins.FQDN.com

` Move keystore file to Jenkins and assign Jenkins service account permissions for same
    cd /var/lib/jenkins
    mkdir keystore
    cp ~/jenkins_keystore.jks /var/lib/jenkins/keystore/
    chown -R jenkins.jenkins keystore/
    chmod 700 keystore/
