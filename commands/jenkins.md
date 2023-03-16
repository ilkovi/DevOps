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