# argocd-example


# Create the K8s cluster
``
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
``

# Install the ArgoCD
```
❯ helm plugin install https://github.com/databus23/helm-diff
Downloading https://github.com/databus23/helm-diff/releases/latest/download/helm-diff-linux-amd64.tgz
Preparing to install into /home/vagrant/.local/share/helm/plugins/helm-diff
Installed plugin: diff

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

❯ kubectl --namespace argocd get secret argocd-initial-admin-secret --output jsonpath='{.data.password}' | base64 -d
<admin password>

❯ ./bin/argocd login localhost:8080
WARNING: server certificate had error: tls: failed to verify certificate: x509: certificate signed by unknown authority. Proceed insecurely (y/n)? y
Username: admin
Password:
'admin:login' logged in successfully
Context 'localhost:8080' updated
```
