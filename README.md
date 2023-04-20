

Docker-Kubernetes-Nginx-Ingress-Dashbord

We will install ingress controller in Minikube, enable ingress that automatically starts the K8s Nginx immplementation of ingress controller.
We will setup name based(hostname) ingress rule and access the dashboard externally. The ingress controller will follow through the rules and forward requests to the kubernetes-dashboard service. Also we will save the IP adress url into /etc/hosts.


## Acknowledgements

 - [Awesome Readme Templates](https://awesomeopensource.com/project/elangosundar/awesome-README-templates)
 - [Awesome README](https://github.com/matiassingers/awesome-readme)
 - [How to write a Good readme](https://bulldogjob.com/news/449-how-to-write-a-good-readme-for-your-github-project)


## Documentation

[Documentation](http://dashboard.info/#/namespace/kubernetes-dashboard?namespace=default)


## Deployment

Create a dashboard-ingress.yaml file

Start Minikube
```bash
$ minikube start --driver=docker
$ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

```
Install Ngnix Ingress controller

```bash
$ minikube addons enable ingress
$ kubectl get pods -n kube-system
NAME                               READY   STATUS    RESTARTS      AGE
coredns-787d4945fb-dj2lv           1/1     Running   4 (39m ago)   36h
etcd-minikube                      1/1     Running   4 (39m ago)   36h
kube-apiserver-minikube            1/1     Running   4 (39m ago)   36h
kube-controller-manager-minikube   1/1     Running   4 (39m ago)   36h
kube-proxy-lzwq5                   1/1     Running   4 (39m ago)   36h
kube-scheduler-minikube            1/1     Running   4 (39m ago)   36h
storage-provisioner                1/1     Running   6 (39m ago)   36h

```
Create Ingress Rules
```bash
$ kubectl get namespace
NAME                   STATUS   AGE
default                Active   38h
ingress-nginx          Active   19h
kube-node-lease        Active   38h
kube-public            Active   38h
kube-system            Active   38h
kubernetes-dashboard   Active   38h

$ kubectl get all -n kubernetes-dashboard
AME                                            READY   STATUS    RESTARTS   AGE
pod/dashboard-metrics-scraper-5c6664855-srld5   1/1     Running   0          3h15m
pod/kubernetes-dashboard-55c4cbbc7c-9q248       1/1     Running   0          3h15m

NAME                                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/dashboard-metrics-scraper   ClusterIP   10.108.155.131   <none>        8000/TCP   38h
service/kubernetes-dashboard        ClusterIP   10.105.86.170    <none>        80/TCP     38h

NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/dashboard-metrics-scraper   1/1     1            1           38h
deployment.apps/kubernetes-dashboard        1/1     1            1           38h

NAME                                                   DESIRED   CURRENT   READY   AGE
replicaset.apps/dashboard-metrics-scraper-5c6664855    1         1         1       38h
replicaset.apps/kubernetes-dashboard-55c4cbbc7c        1         1         1       38h

$ kubectl apply -f dashboard-ingress.yaml
$ kubectl get ingress -n kubernetes-dashboard
NAME                CLASS   HOSTS            ADDRESS        PORTS   AGE
dashboard-ingress   nginx   dashboard.info   192.168.49.2   80      103s
```
Put dashboard info into /etc/hosts

```bash
27.0.0.1       localhost
127.0.1.1       vmmaster
192.168.49.2    dashboard.info
```
Ingress default backend

```bash
kubectl describe ingress dashboard-ingress -n kubernetes-dashboard
oard
Name:             dashboard-ingress
Labels:           <none>
Namespace:        kubernetes-dashboard
Address:          192.168.49.2
Ingress Class:    nginx
Default backend:  <default>
Rules:
  Host            Path  Backends
  ----            ----  --------
  dashboard.info  
                  /   kubernetes-dashboard:80 (10.244.0.19:8443)
Annotations:      <none>
Events:
  Type    Reason  Age                From                      Message
  ----    ------  ----               ----                      -------
  Normal  Sync    48m (x2 over 48m)  nginx-ingress-controller  Scheduled for sync

```

## Screenshots

App Screensho(http://dashboard.info/#/namespace/kubernetes-dashboard?namespace=default)


