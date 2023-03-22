


### Annotations
Annotating a Resource with One Command


ANNOTATION - briefly outline the content


Annotations = key/value pairs to hold nonidentifying information




### Anotation

Annotations are defined in the common metadata section in every Kubernetes
object:
...
metadata:
  annotations:
    example.com/icon-url: "https://example.com/icon.png"
...

Annotations are very convenient and provide powerful loose coupling. However,
they should be used judiciously to avoid an untyped mess of data.




### Example


Problem

You want to annotate a resource with a generic, nonidentifying key/value pair, 
possibly using nonhuman readable data.

Solution

Use the kubectl annotate command:

$ kubectl annotate pods foobar \
description='something that you can use for automation'



For example
when you create a deployment with the kubectl run command and you forget to use the --record option,
 you will notice that the change-cause column in your rollout history is empty


Kubernetes v1.6.0, to start recording the commands that cause changes to the deployment,
you can annotate it with the kubernetes.io/change-cause key

$ kubectl annotate deployment foobar \
kubernetes.io/change-cause="Reason for creating a new revision"
