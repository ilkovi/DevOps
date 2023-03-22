
### Draining Kubernetes Nodes for Maintenance

Use the kubectl drain command. For example, to do maintenance on node 123-worker:

$ kubectl drain 123-worker

When you are ready to put the node back into service, use 

$ kubectl uncordon 123-worker


` NOTE
For pods supervised by a DaemonSet, drain will not proceed without using --ignore-daemonsets,
 and regardless it will not delete any DaemonSet-managed pods—those pods would be immediately 
replaced by the DaemonSet controller, which ignores unschedulable markings

!! WARNING
drain waits for graceful termination, so you should not operate on this node until the 
kubectl drain command has completed. Note that kubectl drain $NODE --force will also 
evict pods not managed by an RC, RS, job, DaemonSet, or StatefulSet.
