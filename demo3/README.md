# Demo 3


## Agenda

* Pesistent Volume
* Persistent Volume Claim
* Environment Variables
* Resources

### Updating demo2 to use all resources in agenda

```sh
# files are present in demo3 folder and we going to use namespace as demo3
$ cd kubectl create -f demo3/ -n demo3

deployment.apps/app created
persistentvolumeclaim/app-claim created
service/app created
persistentvolumeclaim/celery-claim created
deployment.apps/celery-service created
deployment.apps/redis created
service/redis created
storageclass.storage.k8s.io/first-sc created
```

### Check pv and pvc being named and created

```sh
$ kubectl get pvc -n demo3

NAME           STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
app-claim      Bound    pvc-85dc482c-1813-11ea-aadf-4201ac1c0869   1Gi        RWO            first-sc       8m39s
celery-claim   Bound    pvc-86774746-1813-11ea-aadf-4201ac1c0869   1Gi        RWO            first-sc       8m38s

$ kubectl get pv -n demo3

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE
pvc-85dc482c-1813-11ea-aadf-4201ac1c0869   1Gi        RWO            Delete           Bound    demo3/app-claim      first-sc                8m38s
pvc-86774746-1813-11ea-aadf-4201ac1c0869   1Gi        RWO            Delete           Bound    demo3/celery-claim   first-sc                8m37s
```

## Check how environment to view environment variables

```sh
# Login to container and check for env
$ kubectl exec -it <podname> /bin/bash

# Previuos command took you in the container shell
$ echo $<environment_variable> 
```

## Reference for Topics covered in Demo 3

* [Resources](https://kubernetes.io/docs/tasks/configure-pod-container/assign-memory-resource/)
* [PersistentVolume](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
* [PersistentVolumeClaim](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)
* [Environment Variables](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)

