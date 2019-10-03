# NVIDIA GPU Operator

The GPU operator manages NVIDIA GPU resources in a Kubernetes cluster and automates tasks related to bootstrapping GPU nodes. Since the GPU is a special resource in the cluster, it requires a few components to be installed before application workloads can be deployed onto the GPU. These components include the NVIDIA drivers (to enable CUDA), Kubernetes device plugin, container runtime and others such as automatic node labelling, monitoring etc.

## Project Status
This is a technical preview release of the GPU operator. The operator is available on NGC and can be deployed using a Helm chart. 


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

## Prerequisites and Platform Support 
- Pascal+ GPUs are supported (incl. Tesla V100 and T4) 
- Kubernetes v1.13+
- Helm 2
- Ubuntu 18.04.3 LTS
- NFD deployed on each node (see how to [setup](https://github.com/kubernetes-sigs/node-feature-discovery))
  **(only if helm option nfd.enabled is set to false)**
  - Nodes must not be already setup with NVIDIA Components (driver, runtime, device plugin)

## Known Limitations
  - With Kubernetes v1.16, Helm may fail to initialize. See [this issue](https://github.com/helm/helm/issues/6374) for more details.
  - GPU Operator will fail on nodes already setup with NVIDIA Components (driver, runtime, device plugin)
  - Removing the GPU Operator will require you to reboot your nodes

## Contributions
  [Read the document on contributions](https://github.com/NVIDIA/gpu-operator/blob/master/CONTRIBUTING.md). You can contribute by opening a [pull request](https://help.github.com/en/articles/about-pull-requests).

## Getting Help
  Please open [an issue on the GitHub project](https://github.com/NVIDIA/gpu-operator/issues/new) for any questions. Your feedback is appreciated.
