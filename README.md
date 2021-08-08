# Kubernetes Test Case
## Test Blog Site with MongoDB 
Check nodes:
```
kubectl get nodes

NAME                    STATUS   ROLES    AGE    VERSION
kube1                   Ready    <none>   321d   v1.18.2
localhost.localdomain   Ready    master   321d   v1.18.2
```
Create a Name Space:
```
kubectl create namespace blogsite
kubectl get namespace

NAME              STATUS   AGE
blogsite          Active   98s
default           Active   321d
kube-node-lease   Active   321d
kube-public       Active   321d
kube-system       Active   321d
my-ns             Active   320d
```

Create Persistent Volume Storage for MongoDB:

```
kubectl create -n blogsite -f mongodb-pv.yml 
kubectl get -n blogsite pv

mongodb-pv-volume   5Gi        RWO            Retain           Bound    blogsite/mongodb-pv-claim   standard                32s
```

Create MongoDB Deployment:
```
 kubectl create -n blogsite -f mongodb.yml 
 kubectl get -n blogsite pods

 NAME                     READY   STATUS              RESTARTS   AGE
 mongo-74875856c5-mwzdm   0/1     ContainerCreating   0          19s

 kubectl get -n blogsite deployments
 NAME    READY   UP-TO-DATE   AVAILABLE   AGE
 mongo   1/1     1            1           35s
```
Login to MongoDB Pod:
```
kubectl -n blogsite exec -it mongo-74875856c5-mwzdm -- /bin/bash

```
Check to see PV is mounted on MongoDB Pod:
```
root@mongo-74875856c5-mwzdm:/# df -h
Filesystem           Size  Used Avail Use% Mounted on
overlay               50G  4.5G   46G   9% /
tmpfs                 64M     0   64M   0% /dev
tmpfs                915M     0  915M   0% /sys/fs/cgroup
/dev/mapper/cl-root   50G  4.5G   46G   9% /data/db
shm                   64M     0   64M   0% /dev/shm
tmpfs                915M   12K  915M   1% /run/secrets/kubernetes.io/serviceaccount
tmpfs                915M     0  915M   0% /proc/acpi
tmpfs                915M     0  915M   0% /proc/scsi
tmpfs                915M     0  915M   0% /sys/firmware
```
Check MongoDB Pod is working:
````
root@mongo-74875856c5-mwzdm:/# mongo
> show dbs;
admin   0.000GB
config  0.000GB
local   0.000GB
````




Create NodeJS Blog Site:
```
kubectl create -n blogsite -f nodjs-blog-app-pod.yml

kubectl get -n blogsite deployments -o wide

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS   IMAGES                            SELECTOR
mongo                  1/1     1            1           11m    mongo        mongo:latest                      app=mongo
node-blog-deployment   1/1     1            1           110s   nodejsblog   exusiasoftware/nodejs-blog-test   app=node

kubectl get -n blogsite pods -o wide

NAME                                    READY   STATUS    RESTARTS   AGE     IP            NODE    NOMINATED NODE   READINESS GATES
mongo-74875856c5-mwzdm                  1/1     Running   0          13m     10.244.1.15   kube1   <none>           <none>
node-blog-deployment-6f4cc7d7c6-lkvh5   1/1     Running   0          3m49s   10.244.1.16   kube1   <none>           <none>

```
Remove the envorinment

```
kubectl delete -n blogsite deployment mongo
kubectl delete -n blogsite deployment node-blog-deployment
kubectl delete -n blogsite svc nodeservice
kubectl delete -n blogsite svc mongoservice
kubectl delete -n blogsite pvc mongodb-pv-claim
kubectl patch --namespace=blogsite pv mongodb-pv-volume  -p '{"metadata":{"finalizers":null}'

kubectl delete namespace blogsite
```
