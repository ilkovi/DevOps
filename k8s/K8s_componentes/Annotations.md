


### Annotations


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


