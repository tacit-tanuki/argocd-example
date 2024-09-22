# argocd-example

## Setup

### Install required commands
- Install the `task` command
```
sh -c "$(curl --location https://taskfile.dev/install.sh)" -- -d -b ./bin/
```

- Setup the `PATH` environment variable
```
source .envrc
```

- Install required commands
```
task install-commands
```

### Create the K8s cluster
```
❯ kind create cluster --config kind/config.yaml
Creating cluster "argocd-example-cluster" ...
 ✓ Ensuring node image (kindest/node:v1.29.2) 🖼
 ✓ Preparing nodes 📦 📦 📦 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
 ✓ Joining worker nodes 🚜
Set kubectl context to "kind-argocd-example-cluster"
You can now use your cluster with:

kubectl cluster-info --context kind-argocd-example-cluster

Have a nice day! 👋

❯ kubectl get nodes
NAME                                   STATUS   ROLES           AGE     VERSION
argocd-example-cluster-control-plane   Ready    control-plane   2m24s   v1.29.2
argocd-example-cluster-worker          Ready    <none>          2m5s    v1.29.2
argocd-example-cluster-worker2         Ready    <none>          2m5s    v1.29.2
argocd-example-cluster-worker3         Ready    <none>          2m3s    v1.29.2
```

### Install the ArgoCD
```
❯ cd helmfile

❯ helmfile diff
<omit>

❯ helmfile apply
<omit>

❯ kubectl get pod -n argocd
NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          3m16s
argocd-applicationset-controller-5bc7f4ff55-k2f8l   1/1     Running   0          3m16s
argocd-dex-server-5b7c4f9d4f-7hgb2                  1/1     Running   0          3m16s
argocd-notifications-controller-5cbc66fd96-szf28    1/1     Running   0          3m16s
argocd-redis-7cdbbb8576-t67sx                       1/1     Running   0          3m16s
argocd-repo-server-f7b9c9859-rw25v                  1/1     Running   0          3m16s
argocd-server-f9cf5db6c-xk6qx                       1/1     Running   0          3m16s

❯ kubectl port-forward service/argocd-server --namespace argocd 8080:443
Forwarding from 127.0.0.1:8080 -> 8080

❯ kubectl --namespace argocd get secret argocd-initial-admin-secret --output jsonpath='{.data.password}' | base64 -d
<admin password>

❯ argocd login localhost:8080
WARNING: server certificate had error: tls: failed to verify certificate: x509: certificate signed by unknown authority. Proceed insecurely (y/n)? y
Username: admin
Password: <admin password>
'admin:login' logged in successfully
Context 'localhost:8080' updated
```

### Create and sync the ArgoCD application
```
❯ argocd app create guestbook --repo https://github.com/argoproj/argocd-example-apps.git --path guestbook --dest-server https://kubernetes.default.svc --dest-namespace default
application 'guestbook' created

❯ argocd app sync guestbook
TIMESTAMP                  GROUP        KIND   NAMESPACE                  NAME    STATUS    HEALTH        HOOK  MESSAGE
2024-09-22T21:46:18+00:00            Service     default          guestbook-ui  OutOfSync  Missing
2024-09-22T21:46:18+00:00   apps  Deployment     default          guestbook-ui  OutOfSync  Missing
2024-09-22T21:46:19+00:00            Service     default          guestbook-ui    Synced  Healthy
2024-09-22T21:46:19+00:00            Service     default          guestbook-ui    Synced   Healthy              service/guestbook-ui created
2024-09-22T21:46:19+00:00   apps  Deployment     default          guestbook-ui  OutOfSync  Missing              deployment.apps/guestbook-ui created
2024-09-22T21:46:19+00:00   apps  Deployment     default          guestbook-ui    Synced  Progressing              deployment.apps/guestbook-ui created

Name:               argocd/guestbook
Project:            default
Server:             https://kubernetes.default.svc
Namespace:          default
URL:                https://argocd.example.com/applications/guestbook
Source:
- Repo:             https://github.com/argoproj/argocd-example-apps.git
  Target:
  Path:             guestbook
SyncWindow:         Sync Allowed
Sync Policy:        Manual
Sync Status:        Synced to  (d7927a2)
Health Status:      Progressing

Operation:          Sync
Sync Revision:      d7927a27b4533926b7d86b5f249cd9ebe7625e90
Phase:              Succeeded
Start:              2024-09-22 21:46:18 +0000 UTC
Finished:           2024-09-22 21:46:19 +0000 UTC
Duration:           1s
Message:            successfully synced (all tasks run)

GROUP  KIND        NAMESPACE  NAME          STATUS  HEALTH       HOOK  MESSAGE
       Service     default    guestbook-ui  Synced  Healthy            service/guestbook-ui created
apps   Deployment  default    guestbook-ui  Synced  Progressing        deployment.apps/guestbook-ui created

❯ argocd app list
NAME              CLUSTER                         NAMESPACE  PROJECT  STATUS  HEALTH   SYNCPOLICY  CONDITIONS  REPO                                                 PATH       TARGET
argocd/guestbook  https://kubernetes.default.svc  default    default  Synced  Healthy  Manual      <none>      https://github.com/argoproj/argocd-example-apps.git  guestbook

❯ kubectl get all -n default
NAME                                READY   STATUS    RESTARTS   AGE
pod/guestbook-ui-5fbf7fddd6-46kps   1/1     Running   0          4m5s

NAME                   TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/guestbook-ui   ClusterIP   10.96.248.40   <none>        80/TCP    4m5s
service/kubernetes     ClusterIP   10.96.0.1      <none>        443/TCP   64m

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/guestbook-ui   1/1     1            1           4m5s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/guestbook-ui-5fbf7fddd6   1         1         1       4m5s
```

### Access the ArgoCD application
```
❯ kubectl port-forward svc/guestbook-ui --namespace default 8081:80

❯ curl localhost:8081
<omit>
```
