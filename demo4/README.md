# Demo 4


## Agenda

* StatefulSets
* StorageClass
* RollOut
* Secrets

### Setup Cassandra

```sh
# files are present in demo4 folder and we going to use namespace as demo4
$ cd kubectl create -f demo4/ -n demo4

service/cassandra created
storageclass.storage.k8s.io/fast-sc created
statefulset.apps/cassandra created
```

### Watch how pods are being named and created

```sh
$ kubectl get pods -n demo4 | grep cassandra

cassandra-0   1/1     Running   0          17m
cassandra-1   1/1     Running   0          16m
cassandra-2   1/1     Running   0          15m
```

## Check how secret works

```sh
# create a secret with 2 key value pairs
$ kubectl create secret generic test-secret --from-literal=username='user1' --from-literal=password='password'

secret/test-secret created
# this secret can be accessed as environment variable or volume mount. In this sts.yaml we are using it as env var.
```

## Check how rollout works

```sh
# Upgarde Cassndra v12 to v13
$ kubectl patch statefulset cassandra -n demo4 --type='json' -p='[{"op": "replace", "path": "/spec/template/spec/containers/0/image", "value":"gcr.io/google-samples/cassandra:v13"}]'

statefulset.apps/cassandra patched
# Post this - check the pods using 'watch -n1 "kubectl get pods -n demo4 | grep cassandra"'
# It shall provide the way rollout is happening pod per pod and thus ensuring availability of service always
```

## Reference for Topics covered in Demo 4

* [StatefulSets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)
* [Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)
* [Rollout](https://medium.com/platformer-blog/enable-rolling-updates-in-kubernetes-with-zero-downtime-31d7ec388c81)
* [Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)
