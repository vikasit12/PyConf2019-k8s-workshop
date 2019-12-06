# Demo 1

## Agenda

* Cluster information 
* Pods detail 
* Namespaces 
* Run a pod with kubectl
* Deployments 
* Labels and selectors 
* Replicasets
* Expose deployments

### Cluster information
```
arunc@arun:~$ kubectl get nodes
NAME                                           STATUS   ROLES    AGE   VERSION
gke-pyconfhyd-k8s-default-pool-794066aa-3lxr   Ready    <none>   19h   v1.13.11-gke.14
gke-pyconfhyd-k8s-default-pool-794066aa-6q2j   Ready    <none>   19h   v1.13.11-gke.14
gke-pyconfhyd-k8s-default-pool-794066aa-n0f7   Ready    <none>   19h   v1.13.11-gke.14
```
More info:
```
arunc@arun:~$ kubectl get nodes -o wide
NAME                                           STATUS   ROLES    AGE   VERSION           INTERNAL-IP   EXTERNAL-IP      OS-IMAGE                             KERNEL-VERSION   CONTAINER-RUNTIME
gke-pyconfhyd-k8s-default-pool-794066aa-3lxr   Ready    <none>   19h   v1.13.11-gke.14   10.128.0.4    108.59.80.216    Container-Optimized OS from Google   4.14.138+        docker://18.9.7
gke-pyconfhyd-k8s-default-pool-794066aa-6q2j   Ready    <none>   19h   v1.13.11-gke.14   10.128.0.2    35.192.149.247   Container-Optimized OS from Google   4.14.138+        docker://18.9.7
gke-pyconfhyd-k8s-default-pool-794066aa-n0f7   Ready    <none>   19h   v1.13.11-gke.14   10.128.0.3    34.66.182.59     Container-Optimized OS from Google   4.14.138+        docker://18.9.7
```
### Is there anything running in here ?
```
arunc@arun:~$ kubectl get pods
No resources found in pyconfhyd2019-demo1 namespace.
```

### How do i see the containers running ? what exactly are the namespaces?
```
arunc@arun:~$ kubectl get namespaces 
NAME                  STATUS   AGE
default               Active   19h
kube-public           Active   19h
kube-system           Active   19h
``` 
### Which namespaces am in ?
```
arunc@arun:~$ kubectl config get-contexts
CURRENT   NAME                                            CLUSTER                                         AUTHINFO                                        NAMESPACE
*         gke_pyconfhyd2019_us-central1-a_pyconfhyd-k8s   gke_pyconfhyd2019_us-central1-a_pyconfhyd-k8s   gke_pyconfhyd2019_us-central1-a_pyconfhyd-k8s   default
```
### Pods from other namespace

```
arunc@arun:~$ kubectl get pods --namespace kube-system
NAME                                                      READY   STATUS    RESTARTS   AGE
event-exporter-v0.2.4-5f88c66fb7-7q8hc                    2/2     Running   0          19h
fluentd-gcp-scaler-59b7b75cd7-ltb92                       1/1     Running   0          19h
fluentd-gcp-v3.2.0-9hqdm                                  2/2     Running   0          19h
fluentd-gcp-v3.2.0-k2xjn                                  2/2     Running   0          19h
fluentd-gcp-v3.2.0-n964f                                  2/2     Running   0          19h
heapster-ff5bf6fbb-68ksz                                  3/3     Running   0          19h
kube-dns-79868f54c5-bg75d                                 4/4     Running   0          19h
kube-dns-79868f54c5-ng6hq                                 4/4     Running   0          19h
kube-dns-autoscaler-bb58c6784-qd7b5                       1/1     Running   0          19h
kube-proxy-gke-pyconfhyd-k8s-default-pool-794066aa-3lxr   1/1     Running   0          19h
kube-proxy-gke-pyconfhyd-k8s-default-pool-794066aa-6q2j   1/1     Running   0          19h
kube-proxy-gke-pyconfhyd-k8s-default-pool-794066aa-n0f7   1/1     Running   0          19h
l7-default-backend-fd59995cd-znv68                        1/1     Running   0          19h
metrics-server-v0.3.1-57c75779f-kvmxg                     2/2     Running   0          19h
prometheus-to-sd-89hch                                    2/2     Running   0          19h
prometheus-to-sd-nfxmj                                    2/2     Running   0          19h
prometheus-to-sd-w4bmc                                    2/2     Running   0          19h
```

```
arunc@arun:~$ kubectl get pods --namespace kube-public
No resources found in kube-public namespace.
```
```
arunc@arun:~$ kubectl get pods --namespace default
No resources found in default namespace.
```
### How do you spawn a container with Docker?
```
arunc@arun:~$ docker container run --name some-container --image nginx:latest --port 80:80
```
### How about kubectl?
```
arunc@arun:~$ kubectl run some-app --image nginx:latest --port 80
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.  <-- Warnings can be ignored for now
deployment.apps/some-app created <--- lets park this thing aside for now.
```
```
arunc@arun:~$ kubectl get pods
NAME                       READY   STATUS    RESTARTS   AGE
some-app-9f8669565-k7dbd   1/1     Running   0          18s
```

### Hoping into pod
```
arunc@arun:~$ kubectl exec -it some-app-9f8669565-k7dbd /bin/bash

root@some-app-9f8669565-k7dbd:/# hostname
some-app-9f8669565-k7dbd

root@some-app-9f8669565-k7dbd:/# apt update && apt install curl
Get:1 http://cdn-fastly.deb.debian.org/debian buster InRelease [122 kB]             
Get:3 http://cdn-fastly.deb.debian.org/debian buster-updates InRelease [49.3 kB]    
Get:4 http://cdn-fastly.deb.debian.org/debian buster/main amd64 Packages [7908 kB]
Get:2 http://security-cdn.debian.org/debian-security buster/updates InRelease [65.4 kB]
....
....
done.
```
```
root@some-app-9f8669565-k7dbd:/# curl localhost:80   
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
```
arunc@arun:~$ kubectl config get-contexts
CURRENT   NAME                                            CLUSTER                                         AUTHINFO                                        NAMESPACE
*         gke_pyconfhyd2019_us-central1-a_pyconfhyd-k8s   gke_pyconfhyd2019_us-central1-a_pyconfhyd-k8s   gke_pyconfhyd2019_us-central1-a_pyconfhyd-k8s   default
```
### Creating a namespace for your workload.
```
arunc@arun:~$ kubectl create namespace pyconfhyd2019-demo1
namespace/pyconfhyd2019-demo1 created
```

```
arunc@arun:~$ kubectl run some-app --image nginx:latest --port 80 --namespace=pyconfhyd2019-demo1
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/some-app created
```
```
arunc@arun:~$ kubectl get pods --namespace=pyconfhyd2019-demo1
NAME                       READY   STATUS    RESTARTS   AGE
some-app-9f8669565-crbls   1/1     Running   0          21m
```
### Set the current context
```
arunc@arun:~$ kubectl config set-context --current --namespace=pyconfhyd2019-demo1
Context "gke_pyconfhyd2019_us-central1-a_pyconfhyd-k8s" modified.
```
```
arunc@arun:~$ kubectl get deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
some-app   1/1     1            1           24m
```
```
arunc@arun:~$ kubectl describe deployment some-app
Name:                   some-app
Namespace:              pyconfhyd2019-demo1
CreationTimestamp:      Thu, 05 Dec 2019 13:56:26 +0530
Labels:                 run=some-app
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=some-app
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  run=some-app
  Containers:
   some-app:
    Image:        nginx:latest
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   some-app-9f8669565 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  24m   deployment-controller  Scaled up replica set some-app-9f8669565 to 1
```

### Introduction to:
* Labels and Selectors
* Replicas 
* Strategy Types

#### Labels and Selectors
* key/value pair
* core grouping primitive in Kubernetes
* can be used to organize and to select subsets of objects.
```
arunc@arun:~$ kubectl get all -l run=some-app
NAME                           READY   STATUS    RESTARTS   AGE
pod/some-app-9f8669565-crbls   1/1     Running   0          65m

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/some-app   1/1     1            1           65m

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/some-app-9f8669565   1         1         1       65m
```

#### ReplicaSet
* purpose is to maintain a stable set of replica Pods running at any given time
```
arunc@arun:~$ kubectl get replicasets
NAME                 DESIRED   CURRENT   READY   AGE
some-app-9f8669565   1         1         1       78m
```
```
arunc@arun:~$ kubectl get pods
NAME                       READY   STATUS    RESTARTS   AGE
some-app-9f8669565-crbls   1/1     Running   1          88m
```
```
arunc@arun:~$ kubectl delete pod some-app-9f8669565-crbls
pod "some-app-9f8669565-crbls" deleted
```
```
NAME                       READY   STATUS    RESTARTS   AGE
some-app-9f8669565-pwkkj   1/1     Running   0          8s
```
#### Self Healing --- Checked


#### Deployment:
* provides declarative updates for pods and replicasets.
* describe a desired state in a Deployment.
* create new ReplicaSets, or to remove existing Deployments and adopt all their resources with new Deployments.

Resources to refer:
* https://www.weave.works/blog/kubernetes-deployment-strategies
* https://blog.container-solutions.com/kubernetes-deployment-strategies
* https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment

### Scale deployment:
```
arunc@arun:~$ kubectl describe deployment some-app
Name:                   some-app
Namespace:              pyconfhyd2019-demo1
CreationTimestamp:      Thu, 05 Dec 2019 13:56:26 +0530
Labels:                 run=some-app
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=some-app
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  run=some-app
  Containers:
   some-app:
    Image:        nginx:latest
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   some-app-9f8669565 (1/1 replicas created)
Events:          <none>
```
```
arunc@arun:~$ kubectl scale --replicas=3 deployment/some-app
deployment.extensions/some-app scaled
```
```
arunc@arun:~$ kubectl get replicasets
NAME                 DESIRED   CURRENT   READY   AGE
some-app-9f8669565   3         3         3       162m
```
```
arunc@arun:~$ kubectl get pods -o wide
NAME                       READY   STATUS    RESTARTS   AGE     IP           NODE                                           NOMINATED NODE   READINESS GATES
some-app-9f8669565-k7dbd   1/1     Running   0          4h55m   10.28.0.14   gke-pyconfhyd-k8s-default-pool-794066aa-6q2j   <none>           <none>
some-app-9f8669565-lz6xv   1/1     Running   0          18s     10.28.0.20   gke-pyconfhyd-k8s-default-pool-794066aa-6q2j   <none>           <none>
some-app-9f8669565-vvlf7   1/1     Running   0          18s     10.28.1.11   gke-pyconfhyd-k8s-default-pool-794066aa-n0f7   <none>           <none>
```
```
arunc@arun:~$ kubectl get nodes -o wide
NAME                                           STATUS   ROLES    AGE   VERSION           INTERNAL-IP   EXTERNAL-IP      OS-IMAGE                             KERNEL-VERSION   CONTAINER-RUNTIME
gke-pyconfhyd-k8s-default-pool-794066aa-3lxr   Ready    <none>   26h   v1.13.11-gke.14   10.128.0.4    108.59.80.216    Container-Optimized OS from Google   4.14.138+        docker://18.9.7
gke-pyconfhyd-k8s-default-pool-794066aa-6q2j   Ready    <none>   26h   v1.13.11-gke.14   10.128.0.2    35.192.149.247   Container-Optimized OS from Google   4.14.138+        docker://18.9.7
gke-pyconfhyd-k8s-default-pool-794066aa-n0f7   Ready    <none>   26h   v1.13.11-gke.14   10.128.0.3    34.66.182.59     Container-Optimized OS from Google   4.14.138+        docker://18.9.7
```
```
arunc@arun:~$ curl http://35.192.149.247:80
curl: (7) Failed to connect to 35.192.149.247 port 80: Connection refused
```
#### Expose deployment
```
arunc@arun:~$ kubectl expose deployment some-app --type=LoadBalancer --name=some-app-service --port=80
service/some-app-service exposed
```
```
arunc@arun:~$ kubectl get service
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1     <none>        443/TCP        26h
some-app-service   LoadBalancer   10.0.15.53   <pending>     80:30133/TCP   6s
```
```
arunc@arun:~$ kubectl get service
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1     <none>        443/TCP        26h
some-app-service   LoadBalancer   10.0.15.53   34.69.3.148   80:30133/TCP   59s
```
```
arunc@arun:~$ curl http://34.69.3.148:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
```
arunc@arun:~$ kubectl get pods
NAME                       READY   STATUS    RESTARTS   AGE
some-app-9f8669565-k7dbd   1/1     Running   0          5h
some-app-9f8669565-lz6xv   1/1     Running   0          5m37s
some-app-9f8669565-vvlf7   1/1     Running   0          5m37s
```
```
arunc@arun:~$ kubectl exec -it some-app-9f8669565-k7dbd /bin/bash

root@some-app-9f8669565-k7dbd:/# >/usr/share/nginx/html/index.html 

root@some-app-9f8669565-k7dbd:/# echo "This is 1" >> /usr/share/nginx/html/index.html
```

Same way update the index.html for rest of the pods and then try accessing the loadbalancer endpoint.

```
arunc@arun:~$ curl http://34.69.3.148:80
this is 2

arunc@arun:~$ curl http://34.69.3.148:80
this is 3

arunc@arun:~$ curl http://34.69.3.148:80
this is 3

arunc@arun:~$ curl http://34.69.3.148:80
This is 1

arunc@arun:~$ curl http://34.69.3.148:80
This is 1
```
#### 		---- Loadbalancing Checked ----------

```
arunc@arun:~$ kubectl get service
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1     <none>        443/TCP        26h
some-app-service   LoadBalancer   10.0.15.53   34.69.3.148   80:30133/TCP   12m
arunc@arun:~$ kubectl describe service some-app-service
Name:                     some-app-service
Namespace:                default
Labels:                   run=some-app
Annotations:              <none>
Selector:                 run=some-app
Type:                     LoadBalancer
IP:                       10.0.15.53
LoadBalancer Ingress:     34.69.3.148
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30133/TCP
Endpoints:                10.28.0.14:80,10.28.0.20:80,10.28.1.11:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type    Reason                Age        From                Message
  ----    ------                ----       ----                -------
  Normal  EnsuringLoadBalancer  <invalid>  service-controller  Ensuring load balancer
  Normal  EnsuredLoadBalancer   <invalid>  service-controller  Ensured load balancer
```
```
arunc@arun:~$ kubectl get pods -o wide
NAME                       READY   STATUS    RESTARTS   AGE     IP           NODE                                           NOMINATED NODE   READINESS GATES
some-app-9f8669565-k7dbd   1/1     Running   0          5h11m   10.28.0.14   gke-pyconfhyd-k8s-default-pool-794066aa-6q2j   <none>           <none>
some-app-9f8669565-lz6xv   1/1     Running   0          16m     10.28.0.20   gke-pyconfhyd-k8s-default-pool-794066aa-6q2j   <none>           <none>
some-app-9f8669565-vvlf7   1/1     Running   0          16m     10.28.1.11   gke-pyconfhyd-k8s-default-pool-794066aa-n0f7   <none>           <none>
```
```
arunc@arun:~$ kubectl scale --replicas=5 deployment/some-app
deployment.extensions/some-app scaled
```
```
arunc@arun:~$ kubectl get pods -o wide
NAME                       READY   STATUS    RESTARTS   AGE     IP           NODE                                           NOMINATED NODE   READINESS GATES
some-app-9f8669565-65jvq   1/1     Running   0          7s      10.28.2.9    gke-pyconfhyd-k8s-default-pool-794066aa-3lxr   <none>           <none>
some-app-9f8669565-k7dbd   1/1     Running   0          5h12m   10.28.0.14   gke-pyconfhyd-k8s-default-pool-794066aa-6q2j   <none>           <none>
some-app-9f8669565-lsb7h   1/1     Running   0          7s      10.28.1.12   gke-pyconfhyd-k8s-default-pool-794066aa-n0f7   <none>           <none>
some-app-9f8669565-lz6xv   1/1     Running   0          17m     10.28.0.20   gke-pyconfhyd-k8s-default-pool-794066aa-6q2j   <none>           <none>
some-app-9f8669565-vvlf7   1/1     Running   0          17m     10.28.1.11   gke-pyconfhyd-k8s-default-pool-794066aa-n0f7   <none>           <none>
```
```
arunc@arun:~$ kubectl describe service some-app-service
Name:                     some-app-service
Namespace:                default
Labels:                   run=some-app
Annotations:              <none>
Selector:                 run=some-app
Type:                     LoadBalancer
IP:                       10.0.15.53
LoadBalancer Ingress:     34.69.3.148
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30133/TCP
Endpoints:                10.28.0.14:80,10.28.0.20:80,10.28.1.11:80 + 2 more...
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type    Reason                Age        From                Message
  ----    ------                ----       ----                -------
  Normal  EnsuringLoadBalancer  <invalid>  service-controller  Ensuring load balancer
  Normal  EnsuredLoadBalancer   <invalid>  service-controller  Ensured load balancer
```
```
arunc@arun:~$ curl http://34.69.3.148:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

```
arunc@arun:~$ curl http://34.69.3.148:80
This is 1
```
#### ------------That's how  Service Discovery is happening -------------------

#### What is service and type above ?

#### Service: 

* An abstract way to expose an application running on a set of Pods as a network service.

* Use Case: if some set of Pods (call them “backends”) provides functionality to other Pods (call them “frontends”) inside your cluster, how do the frontends find out and keep track of which IP address to connect to, so that the frontend can use the backend part of the workload?


#### Service Type: 

 * Kubernetes ServiceTypes allow you to specify what kind of Service you want. The default is ClusterIP.

 * Type values and their behaviors are:

	 * **ClusterIP**: Exposes the Service on a cluster-internal IP. Choosing this value makes the Service only reachable from within the cluster. This is the default ServiceType. 
	 * **NodePort**: Exposes the Service on each Node’s IP at a static port (the NodePort). A ClusterIP Service, to which the NodePort Service routes, is automatically created. You’ll be able to contact the NodePort Service, from outside the cluster, by requesting ```<NodeIP>:<NodePort>```
	 *  **LoadBalancer**: Exposes the Service externally using a cloud provider’s load balancer.


