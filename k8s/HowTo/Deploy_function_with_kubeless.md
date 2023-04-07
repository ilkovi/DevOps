
###  Deploy ( python ) Function with kubeless

You want to deploy Python, Node.js, Ruby, or PowerShell functions to Kubernetes without having to build a Docker container. You also want to be able to call those functions via HTTP or by sending events to a message bus.

Use the kubeless - kubernetes native surverles solution.


kubeless uses a CustomResourceDefinition 
to define Function objects and a controller to deploy these functions inside pods within a Kubernetes cluster.


1. Create a kubeless namespace and lunch the controller

$ curl -sL https://github.com/kubeless/kubeless/releases/download/v0.3.1/ \
           kubeless-rbac-v0.3.1.yaml | kubectl create -f -

$ wget https://github.com/kubeless/kubeless/releases/download/v0.3.1/ \
       kubeless_darwin-amd64.zip

$ sudo cp bundles/kubeless_darwin-amd64/kubeless /usr/local/bin


Within the kubeless namespace, you will see three pods:
- the controller that watches the Function custom endpoints
- Kafka		- only needed for functions that are triggered by events
- Zookeeper	- only needed for functions that are triggered by events



For HTTP-triggered functions, you only need the controller to be in the running state:
$ kubectl get pods -n kubeless
NAME                                  READY     STATUS    RESTARTS   AGE
kafka-0                               1/1       Running   0          6m
kubeless-controller-9bff848c4-gnl7d   1/1       Running   0          6m
zoo-0                                 1/1       Running   0          6m




2. Crate a python script

def handler(context):
    print context.json
    return context.json



3. Deploy the script into python

kubeless deploy have severan options
--runtime option specifies what language the function is written in;
--http-trigger option specifies that the function will be triggered via HTTP(S) calls
--handler option specifies the name of the function
--from-file option specifies the file in which the function is written:

$ kubeless function deploy post-python --trigger-http \
                                       --runtime python2.7 \
                                       --handler post.handler \
                                       --from-file post.py



4. List the Function

$ kubeless function ls
NAME                 NAMESPACE	HANDLER      RUNTIME  	 TYPE	 TOPIC
post-python	default 	hellowithdata.handler 	python     2.7	  HTTP




5. Location of the function

The kubeless controller detected the new Function object and created a deployment for it.
The function code is stored in a config map (see Recipe 8.3) 
and injected into the running pod at runtime.

$ kubectl get deployments
NAME          DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
post-python   1         1         1            1           2m


$ kubectl get pods
NAME                           READY   STATUS    RESTARTS   AGE
post-python-5bcb9f7d86-d7nbt   1/1     Running   0          6s

$ kubectl get cm
NAME          DATA      AGE
post-python   3         2m

$ kubectl get functions
NAME          AGE
post-python   2m





6. Call the function

$ kubeless function call post-python --data '{"oreilly":"function"}'
{"oreilly": "function"}


NOTE
kubeless can be used for much more than basic HTTP-triggered functions. 
Use the --help option to explore the CLI: kubeless --help.

