# NVIDIA GPU Operator

The GPU operator manages NVIDIA GPU resources in a Kubernetes cluster and automates tasks related to bootstrapping GPU nodes. Since the GPU is a special resource in the cluster, it requires a few components to be installed before application workloads can be deployed onto the GPU. These components include the NVIDIA drivers (to enable CUDA), Kubernetes device plugin, container runtime and others such as automatic node labelling, monitoring etc.


#### Quickstart
```sh
# Install helm https://docs.helm.sh/using_helm/ then run:
helm repo add nvidia https://nvidia.github.io/gpu-operator
helm repo update

helm install nvidia/gpu-operator -n test-operator --wait
kubectl apply -f https://raw.githubusercontent.com/NVIDIA/gpu-operator/master/manifests/cr/sro_cr_sched_none.yaml
```

#### Install Helm
```sh
curl -L https://git.io/get_helm.sh | bash
kubectl create serviceaccount -n kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller

# See: https://github.com/helm/helm/issues/6374
helm init --service-account tiller --override spec.selector.matchLabels.'name'='tiller',spec.selector.matchLabels.'app'='helm' --output yaml | sed 's@apiVersion: extensions/v1beta1@apiVersion: apps/v1@' | kubectl apply -f -
kubectl wait --for=condition=available -n kube-system deployment tiller-deploy

```
