
### Labels

Lables = key/value pairs that can be attached to k8s objects Pods and ReplicaSets
	 K8s use labels to deal with set of objects (no a single instance)

$ kubectl label deployments alpaca-test "canary=true" 	# label
$ kubectl get deployments -L canary			# check
$ kubectl label deployments alpaca-test "canary-" 	# remove




# EXAMPLE:

$ kubectl run alpaca-prod \
--image=gcr.io/kuar-demo/kuard-amd64:1 \
--replicas=2 \
--labels="ver=1,app=alpaca,env=prod"


$ kubectl run alpaca-test \
--image=gcr.io/kuar-demo/kuard-amd64:2 \
--replicas=1 \
--labels="ver=2,app=alpaca,env=test"



$ kubectl run bandicoot-prod \
--image=gcr.io/kuar-demo/kuard-amd64:2 \
--replicas=2 \
--labels="ver=2,app=bandicoot,env=prod"


$ kubectl run bandicoot-staging \
--image=gcr.io/kuar-demo/kuard-amd64:2 \
--replicas=1 \
--labels="ver=2,app=bandicoot,env=staging"


$ kubectl get deployments --show-labels
NAME ... LABELS
alpaca-prod ... app=alpaca,env=prod,ver=1
alpaca-test ... app=alpaca,env=test,ver=2
bandicoot-prod ... app=bandicoot,env=prod,ver=2
bandicoot-staging ... app=bandicoot,env=staging,ver=2





## more EXAMPLES


kubectl get, noting the following:

•-l is the short form of --selector and will query objects with a specified key=value pair.
•--show-labels will show all the labels of each object returned.
•-L will add a column to the results returned with the value of the specified label.


$ kubectl get pods -l 'env in (production, development)'



With two pods running, one with label run=barfoo and the other with label run=foobar

$ kubectl get pods --show-labels
NAME                      READY   ...    LABELS
barfoo-76081199-h3gwx     1/1     ...    pod-template-hash=76081199,run=barfoo
foobar-1123019601-6x9w1   1/1     ...    pod-template-hash=1123019601,run=foobar

$ kubectl get pods -Lrun
NAME                      READY   ...    RUN
barfoo-76081199-h3gwx     1/1     ...    barfoo
foobar-1123019601-6x9w1   1/1     ...    foobar

$ kubectl get pods -l run=foobar
NAME                      READY   ...
foobar-1123019601-6x9w1   1/1     ...





### LABEL SELECTOR = used to filter Kubernetes objects based on a set of labels.

$ kubectl get pods --show-labels

- by default k8s set lable pod-template-hash
  ( to keep track which pod is from which template )


# we only wanted to list pods that had the ver label set to 2

$ kubectl get pods --selector="ver=2"
NAME READY STATUS RESTARTS AGE
alpaca-test-1004512375-3r1m5 1/1 Running 0 3m
bandicoot-prod-373860099-0t1gp 1/1 Running 0 3m
bandicoot-prod-373860099-k2wcf 1/1 Running 0 3m
bandicoot-staging-1839769971-3ndv5 1/1 Running 0 3m



# If we specify two selectors separated by a comma, only the objects that satisfy both will be returned. This is a logical AND operation

$ kubectl get pods --selector="app=bandicoot,ver=2"

$ kubectl get pods --selector="app in (alpaca,bandicoot)"


## same but in yaml

A selector of app=alpaca,ver in (1, 2) would be converted to this:
selector:
  matchLabels:
    app: alpaca
  matchExpressions:
    - {key: ver, operator: In, values: [1, 2]}

		(the last one is a map)
