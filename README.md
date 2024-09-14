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
