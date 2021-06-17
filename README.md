# GitOps: The deployment in prod with git push

This repository is attended to be used for the CloudOuest 2021 conference as a playground.

## Create a sandbox cluster with Kind tool

Download [Kind](https://kind.sigs.k8s.io/)

For this demo we will used this sample configuration (available at ./kind/config.yml) which will create one Main node and two Workers nodes (which is far sufficent to our needs, feel free to add more if you are able to 🙂 )

```bash
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
name: git-ops-demo
nodes:
- role: control-plane
- role: worker
- role: worker
```

Set the Kind Kubernetes context:

```bash
kubectl cluster-info --context kind-git-ops-demo
```

You are ready to start 🎉

## Flux config

### Create Namespace
 
```bash
kubectl create ns flux-demo # We be used to host FlucCD component (Flux, HelmOperator)

kubectl create ns app # The application namespace
```

### Install Flux & HelmRelease

```bash
helm repo add fluxcd https://charts.fluxcd.io # Add the FluxCD chart repository
```

```bash
# Install flux with git-ops-demo repository as source
helm upgrade -i flux fluxcd/flux --wait \
--namespace flux-demo \
--set git.url=git@github.com:scharret/git-ops-demo \
--set sync.interval=30s \
--set git.branch=main \
--set git.path=applications

# Get the SSH Public Key to allow Flux to communicate with your source repository.
# In your git repository, go to settings -> deploy keys -> create a deploy key with write access
kubectl -n flux-demo logs deployment/flux | grep identity.pub | cut -d '"' -f2
```

```bash
# Install HelmOperator CRDs
kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/master/deploy/crds.yaml
```

```bash
# Install HelmOperator Service
helm upgrade -i helm-operator fluxcd/helm-operator --wait \
--namespace flux-demo \
--set git.ssh.secretName=flux-git-deploy \
--set helm.versions=v3
```

### (Optional) Get Deploy Key And add it as deploy key in Github repository

```bash
# You can get your Public Key directly by using fluxctl
fluxctl identity --k8s-fwd-ns fluxcd
