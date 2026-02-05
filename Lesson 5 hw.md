----->


__Part 1 \
__



* A namespace is a way to organise clusters into virtual sub-clusters.
* It’s considered logical separation because they are used within one cluster, to provide organisation of resources in that cluster.

__Part 2__

yuliaa@yuliaa-Latitude-5430:~$ kubectl delete pod demo-pod -n dev

pod "demo-pod" deleted from dev namespace



* If you delete the pod it’s permanently deleted. Because we didn’t have any deploy, or replicaset or controller watching over it. If the pod kind was Deployment, then it would have a controller, and would be recreated automatically.

__Part 3__

yuliaa@yuliaa-Latitude-5430:~$ touch app-deployment.yaml

yuliaa@yuliaa-Latitude-5430:~$ vi app-deployment.yaml

yuliaa@yuliaa-Latitude-5430:~$ kubectl apply -f app-deployment.yaml

deployment.apps/app-deployment created

yuliaa@yuliaa-Latitude-5430:~$ kubectl get pods -n dev

NAME                              READY   STATUS    RESTARTS   AGE

app-deployment-5d879fb8d9-d9zbn   1/1     Running   0          19s

app-deployment-5d879fb8d9-dhlz5   1/1     Running   0          19s

app-deployment-5d879fb8d9-kkws9   1/1     Running   0          19s

yuliaa@yuliaa-Latitude-5430:~$ kubectl delete pod app-deployment-5d879fb8d9-d9zbn -n dev

pod "app-deployment-5d879fb8d9-d9zbn" deleted from dev namespace

yuliaa@yuliaa-Latitude-5430:~$ kubectl get pods -n dev

NAME                              READY   STATUS    RESTARTS   AGE

app-deployment-5d879fb8d9-dhlz5   1/1     Running   0          2m41s

app-deployment-5d879fb8d9-kkws9   1/1     Running   0          2m41s

app-deployment-5d879fb8d9-vtfdq   1/1     Running   0          9s

yuliaa@yuliaa-Latitude-5430:~$ kubectl get deployments,rs,pods -n dev

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE

deployment.apps/app-deployment   3/3     3            3           4m13s

NAME                                        DESIRED   CURRENT   READY   AGE

replicaset.apps/app-deployment-5d879fb8d9   3         3         3       4m13s

NAME                                  READY   STATUS    RESTARTS   AGE

pod/app-deployment-5d879fb8d9-dhlz5   1/1     Running   0          4m13s

pod/app-deployment-5d879fb8d9-kkws9   1/1     Running   0          4m13s

pod/app-deployment-5d879fb8d9-vtfdq   1/1     Running   0          101s



* Deployment ensures the number of pods by managing RepliceSet
* Pod itself does not have the ability to scale, or heal itself, therefore it should not be managed directly.

__Part 4 __

yuliaa@yuliaa-Latitude-5430:~$ kubectl scale deployment app-deployment --replicas=5 -n dev

deployment.apps/app-deployment scaled

yuliaa@yuliaa-Latitude-5430:~$ kubectl set image deployment/app-deployment app=nginx:latest -n dev

deployment.apps/app-deployment image updated

yuliaa@yuliaa-Latitude-5430:~$ kubectl get deployments -n dev

NAME             READY   UP-TO-DATE   AVAILABLE   AGE

app-deployment   5/5     5            5           11m



* New ReplicaSet is created when Pod template of Deployment changes.

__Part 5__



* ClusterIP is Internal only
* For production it’s best to use LoadBalancer

__Part 6__



* Ingress does not work without the controller. Without the controller the rules are ignored.
* Exposing every service directly is unsafe and can cost a lot of money.

__Part 7__



* Config is separated from images so the same container image can be reused in different environments without rebuilding,
* Secrets should be protected with RBAC to ensure that only authorized users and services can access sensitive information such as passwords and tokens.

__Part 8__



* RBAC is namespace-scoped for isolation and safety. By confining permissions to specific namespaces using Roles and RoleBindings, teams can manage workloads independently without interfering with others or accessing cluster-wide resource
* Give only the minimum permissions required to do the job.

__Part 9__ \




* Dev has fever replicas, loose resource limits, more debugging. In prod we have multiple replicas, CPU and memory limits.
* Because clusters become unpredictable, nodes unstable and one pod can consume all the memory.

__CLI output Parts 5 - 9 __ \
 \
yuliaa@yuliaa-Latitude-5430:~$ touch app-ingress.yaml

yuliaa@yuliaa-Latitude-5430:~$ vi app-ingress.yaml

yuliaa@yuliaa-Latitude-5430:~$ kubectl apply -f app-ingress.yaml

ingress.networking.k8s.io/app-ingress created

yuliaa@yuliaa-Latitude-5430:~$ touch app-config.yaml

yuliaa@yuliaa-Latitude-5430:~$ vi app-config.yaml

yuliaa@yuliaa-Latitude-5430:~$ kubectl apply -f app-config.yaml

configmap/app-config created

yuliaa@yuliaa-Latitude-5430:~$ touvh app-secret.yaml

Command 'touvh' not found, did you mean:

  command 'touch' from deb coreutils (8.32-4.1ubuntu1.2)

Try: sudo apt install &lt;deb name>

yuliaa@yuliaa-Latitude-5430:~$ touch app-secret.yaml

yuliaa@yuliaa-Latitude-5430:~$ vi app-secret.yaml

yuliaa@yuliaa-Latitude-5430:~$ kubectl apply -f app-secret.yaml

secret/app-secret created

yuliaa@yuliaa-Latitude-5430:~$ touch sa-role-rbac.yaml

yuliaa@yuliaa-Latitude-5430:~$ vi sa-role-rbac.yaml

yuliaa@yuliaa-Latitude-5430:~$ kubectl apply -f sa-role-rbac.yaml

serviceaccount/app-sa created

yuliaa@yuliaa-Latitude-5430:~$ touch app-role.yaml

yuliaa@yuliaa-Latitude-5430:~$ touch app-rolebinding.yaml

yuliaa@yuliaa-Latitude-5430:~$ vi app-role.yaml

yuliaa@yuliaa-Latitude-5430:~$ vi app-rolebinding.yaml

yuliaa@yuliaa-Latitude-5430:~$ kubectl apply -f app-role.yaml

role.rbac.authorization.k8s.io/pod-reader created

yuliaa@yuliaa-Latitude-5430:~$ kubectl apply -f app-rolebinding.yaml

rolebinding.rbac.authorization.k8s.io/pod-reader-binding created

yuliaa@yuliaa-Latitude-5430:~$ kubectl get pods -n dev

NAME                              READY   STATUS    RESTARTS   AGE

app-deployment-79c685c976-6qrwb   1/1     Running   0          12m

app-deployment-79c685c976-drkjm   1/1     Running   0          12m

app-deployment-79c685c976-m4mph   1/1     Running   0          12m

yuliaa@yuliaa-Latitude-5430:~$ kubectl get services -n dev

NAME          TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE

app-service   ClusterIP   10.103.30.0   &lt;none>        80/TCP    13m

yuliaa@yuliaa-Latitude-5430:~$ kubectl get deployments -n dev

NAME             READY   UP-TO-DATE   AVAILABLE   AGE

app-deployment   3/3     3            3           24h

yuliaa@yuliaa-Latitude-5430:~$ kubectl get ingress -n dev

NAME          CLASS    HOSTS        ADDRESS   PORTS   AGE

app-ingress   &lt;none>   demo.local             80      7m10s

yuliaa@yuliaa-Latitude-5430:~$ kubectl get configmaps -n dev

NAME               DATA   AGE

app-config         2      5m56s

kube-root-ca.crt   1      5d1h

yuliaa@yuliaa-Latitude-5430:~$ kubectl get secrets -n dev

NAME         TYPE     DATA   AGE

app-secret   Opaque   1      5m7s

yuliaa@yuliaa-Latitude-5430:~$ kubectl get serviceaccounts -n dev

NAME      SECRETS   AGE

app-sa    0         3m13s

default   0         5d1h

yuliaa@yuliaa-Latitude-5430:~$ kubectl get roles -n dev

NAME         CREATED AT

pod-reader   2026-01-31T19:49:05Z

yuliaa@yuliaa-Latitude-5430:~$ kubectl get rolebindings -n dev

NAME                 ROLE              AGE

pod-reader-binding   Role/pod-reader   2m7s

__Bonus__

yuliaa@yuliaa-Latitude-5430:~$ touch full-deploy.yaml

yuliaa@yuliaa-Latitude-5430:~$ vi full-deploy.yaml

yuliaa@yuliaa-Latitude-5430:~$ kubectl apply -f full-deploy.yaml

namespace/dev unchanged

deployment.apps/app-deployment configured

service/app-service created

yuliaa@yuliaa-Latitude-5430:~$ kubectl get deployments -n dev

NAME             READY   UP-TO-DATE   AVAILABLE   AGE

app-deployment   3/3     3            3           24h

yuliaa@yuliaa-Latitude-5430:~$ kubectl get pods -n dev

NAME                              READY   STATUS    RESTARTS   AGE

app-deployment-58b5d5cf76-457v7   1/1     Running   0          34s

app-deployment-58b5d5cf76-9r6wc   1/1     Running   0          28s

app-deployment-58b5d5cf76-g4hw7   1/1     Running   0          31s

yuliaa@yuliaa-Latitude-5430:~$ kubectl get services -n dev

NAME          TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE

app-service   ClusterIP   10.103.30.0   &lt;none>        80/TCP    43s

yuliaa@yuliaa-Latitude-5430:~$ kubectl set image deployment/app-deployment app=nginx:alpine -n dev

deployment.apps/app-deployment image updated

yuliaa@yuliaa-Latitude-5430:~$ kubectl get pods -n dev -w

NAME                              READY   STATUS              RESTARTS   AGE

app-deployment-58b5d5cf76-457v7   1/1     Running             0          67s

app-deployment-58b5d5cf76-9r6wc   1/1     Running             0          61s

app-deployment-58b5d5cf76-g4hw7   1/1     Running             0          64s

app-deployment-79c685c976-drkjm   0/1     ContainerCreating   0          7s

app-deployment-79c685c976-drkjm   1/1     Running             0          8s

app-deployment-58b5d5cf76-g4hw7   1/1     Terminating         0          65s

app-deployment-58b5d5cf76-g4hw7   1/1     Terminating         0          65s

app-deployment-79c685c976-6qrwb   0/1     Pending             0          0s

app-deployment-79c685c976-6qrwb   0/1     Pending             0          0s

app-deployment-79c685c976-6qrwb   0/1     ContainerCreating   0          0s

app-deployment-58b5d5cf76-g4hw7   0/1     Completed           0          65s

app-deployment-58b5d5cf76-g4hw7   0/1     Completed           0          66s

app-deployment-58b5d5cf76-g4hw7   0/1     Completed           0          66s

app-deployment-79c685c976-6qrwb   1/1     Running             0          3s

app-deployment-58b5d5cf76-9r6wc   1/1     Terminating         0          65s

app-deployment-58b5d5cf76-9r6wc   1/1     Terminating         0          65s

app-deployment-79c685c976-m4mph   0/1     Pending             0          0s

app-deployment-79c685c976-m4mph   0/1     Pending             0          0s

app-deployment-79c685c976-m4mph   0/1     ContainerCreating   0          0s

app-deployment-58b5d5cf76-9r6wc   0/1     Completed           0          65s

app-deployment-58b5d5cf76-9r6wc   0/1     Completed           0          66s

app-deployment-58b5d5cf76-9r6wc   0/1     Completed           0          66s

app-deployment-79c685c976-m4mph   1/1     Running             0          3s

app-deployment-58b5d5cf76-457v7   1/1     Terminating         0          74s

app-deployment-58b5d5cf76-457v7   1/1     Terminating         0          74s

app-deployment-58b5d5cf76-457v7   0/1     Completed           0          74s

app-deployment-58b5d5cf76-457v7   0/1     Completed           0          75s

app-deployment-58b5d5cf76-457v7   0/1     Completed           0          75s
