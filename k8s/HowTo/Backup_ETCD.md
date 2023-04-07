### BACKUP ETCD - for api verseion 2

IMPORTANT
Up until Kubernetes 1.5.2 we used etcd2, and from then on we switched to etcd3

etcd is a key/value store; in etcd2 the keys formed a hierarchy, 
but with the introduction of etcd3 this was replaced with a flat 
model (while maintaining backwards compatibility concerning hierarchical keys).

$  curl 127.0.0.1:2379/v2/keys/registry | jq .


` NOTE
This technique can be used in environments where etcd is used with the v2 API.
