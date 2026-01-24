
# Kubernetes Minikube Homework

yuliaa@yuliaa-Latitude-5430:~$ kubectl version --client
Client Version: v1.34.3
Kustomize Version: v5.7.1

yuliaa@yuliaa-Latitude-5430:~$ minikube version
minikube version: v1.37.0
commit: 65318f4cfff9c12cc87ec9eb8f4cdd57b25047f3

yuliaa@yuliaa-Latitude-5430:~$ kubectl cluster-info
Kubernetes control plane is running at https://192.168.49.2:8443
CoreDNS is running at https://192.168.49.2:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
yuliaa@yuliaa-Latitude-5430:~$ kubectl get nodes
NAME       STATUS   ROLES           AGE    VERSION
minikube   Ready    control-plane   5d1h   v1.34.0
yuliaa@yuliaa-Latitude-5430:~$ kubectl get namespaces
NAME                   STATUS   AGE
default                Active   5d1h
kube-node-lease        Active   5d1h
kube-public            Active   5d1h
kube-system            Active   5d1h
kubernetes-dashboard   Active   5d
yuliaa@yuliaa-Latitude-5430:~$ kubectl get pods -n kube-system
NAME                               READY   STATUS    RESTARTS      AGE
coredns-66bc5c9577-mc6qw           1/1     Running   2 (76s ago)   5d1h
etcd-minikube                      1/1     Running   2 (74s ago)   5d1h
kube-apiserver-minikube            1/1     Running   2 (71s ago)   5d1h
kube-controller-manager-minikube   1/1     Running   2 (81s ago)   5d1h
kube-proxy-vgts6                   1/1     Running   2 (81s ago)   5d1h
kube-scheduler-minikube            1/1     Running   2 (81s ago)   5d1h
storage-provisioner                1/1     Running   3 (81s ago)   5d1h
yuliaa@yuliaa-Latitude-5430:~$ 

yuliaa@yuliaa-Latitude-5430:~$ kubectl get services -A
NAMESPACE              NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                  AGE
default                kubernetes                  ClusterIP   10.96.0.1        <none>        443/TCP                  7h57m
kube-system            kube-dns                    ClusterIP   10.96.0.10       <none>        53/UDP,53/TCP,9153/TCP   5d1h
kubernetes-dashboard   dashboard-metrics-scraper   ClusterIP   10.107.210.140   <none>        8000/TCP                 5d1h
kubernetes-dashboard   kubernetes-dashboard        ClusterIP   10.108.182.8     <none>        80/TCP                   5d1h

What’s a kubernetes service?
It’s a consistent endpoint with IP address that routes traffic to the right pods, because pods can change. Service provides stable network access and load balancing. It groups pods using labels.


yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl apply -f backend-deployment.yaml
deployment.apps/backend created
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl apply -f backend-service.yaml
service/backend created
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl apply -f frontend-service.yaml
service/frontend created
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl apply -f frontend-deployment.yaml
deployment.apps/frontend created

yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl get deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
backend    1/1     1            1           111s
frontend   1/1     1            1           72s
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
backend-576ccdb8d-qqjkw     1/1     Running   0          118s
frontend-7b9bcbbfc4-vbg7x   1/1     Running   0          79s
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl get services
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
backend      ClusterIP   10.104.43.127   <none>        80/TCP         114s
frontend     NodePort    10.110.85.144   <none>        80:30294/TCP   98s
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        9h
yuliaa@yuliaa-Latitude-5430:~/k8s$ 

yuliaa@yuliaa-Latitude-5430:~/k8s$ minikube service frontend
┌───────────┬──────────┬─────────────┬───────────────────────────┐
│ NAMESPACE │   NAME   │ TARGET PORT │            URL            │
├───────────┼──────────┼─────────────┼───────────────────────────┤
│ default   │ frontend │ 80          │ http://192.168.49.2:30294 │
└───────────┴──────────┴─────────────┴───────────────────────────┘
🎉  Opening service default/frontend in default browser...
Opening in existing browser session.




Part 7

yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl describe deployment frontend
Name:                   frontend
Namespace:              default
CreationTimestamp:      Sat, 24 Jan 2026 21:50:53 +0200
Labels:                 <none>
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=frontend
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=frontend
  Containers:
   frontend:
    Image:         nginx:alpine
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   frontend-7b9bcbbfc4 (1/1 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  7m52s  deployment-controller  Scaled up replica set frontend-7b9bcbbfc4 from 0 to 1
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
backend-576ccdb8d-qqjkw     1/1     Running   0          8m52s
frontend-7b9bcbbfc4-vbg7x   1/1     Running   0          8m13s
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl describe pod backend-576ccdb8d-qqjkw
Name:             backend-576ccdb8d-qqjkw
Namespace:        default
Priority:         0
Service Account:  default
Node:             minikube/192.168.49.2
Start Time:       Sat, 24 Jan 2026 21:50:14 +0200
Labels:           app=backend
                  pod-template-hash=576ccdb8d
Annotations:      <none>
Status:           Running
IP:               10.244.0.22
IPs:
  IP:           10.244.0.22
Controlled By:  ReplicaSet/backend-576ccdb8d
Containers:
  backend:
    Container ID:   docker://2481d1369a34415b855c53652330260a44144d7b8d13c812f563ee6cb832cd8a
    Image:          nginxdemos/hello
    Image ID:       docker-pullable://nginxdemos/hello@sha256:b28d1e16c676896aaa98ed9c36d406cc4f4289558487f15e37e04354720f807a
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sat, 24 Jan 2026 21:50:20 +0200
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-9d2wv (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  kube-api-access-9d2wv:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  9m15s  default-scheduler  Successfully assigned default/backend-576ccdb8d-qqjkw to minikube
  Normal  Pulling    9m15s  kubelet            Pulling image "nginxdemos/hello"
  Normal  Pulled     9m9s   kubelet            Successfully pulled image "nginxdemos/hello" in 5.778s (5.778s including waiting). Image size: 52556834 bytes.
  Normal  Created    9m9s   kubelet            Created container: backend
  Normal  Started    9m9s   kubelet            Started container backend
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl logs backend-576ccdb8d-qqjkw
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: /etc/nginx/conf.d/default.conf is not a file or does not exist
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/01/24 19:50:20 [notice] 1#1: using the "epoll" event method
2026/01/24 19:50:20 [notice] 1#1: nginx/1.29.1
2026/01/24 19:50:20 [notice] 1#1: built by gcc 14.2.0 (Alpine 14.2.0) 
2026/01/24 19:50:20 [notice] 1#1: OS: Linux 6.8.0-90-generic
2026/01/24 19:50:20 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2026/01/24 19:50:20 [notice] 1#1: start worker processes
2026/01/24 19:50:20 [notice] 1#1: start worker process 20
2026/01/24 19:50:20 [notice] 1#1: start worker process 21
2026/01/24 19:50:20 [notice] 1#1: start worker process 22
2026/01/24 19:50:20 [notice] 1#1: start worker process 23
2026/01/24 19:50:20 [notice] 1#1: start worker process 24
2026/01/24 19:50:20 [notice] 1#1: start worker process 25
2026/01/24 19:50:20 [notice] 1#1: start worker process 26
2026/01/24 19:50:20 [notice] 1#1: start worker process 27
2026/01/24 19:50:20 [notice] 1#1: start worker process 28
2026/01/24 19:50:20 [notice] 1#1: start worker process 29
2026/01/24 19:50:20 [notice] 1#1: start worker process 30
2026/01/24 19:50:20 [notice] 1#1: start worker process 31

Part 8
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl delete -f .
deployment.apps "backend" deleted from default namespace
service "backend" deleted from default namespace
deployment.apps "frontend" deleted from default namespace
service "frontend" deleted from default namespace

Part 9
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl get pods
No resources found in default namespace.
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl get deployments
No resources found in default namespace.
yuliaa@yuliaa-Latitude-5430:~/k8s$ kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   9h
 





