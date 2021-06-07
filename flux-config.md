# Create Namespace

```bash
kubectl create ns flux-demo

kubectl create ns app
```

# Install Flux & HelmRelease

```bash
helm repo add fluxcd https://charts.fluxcd.io
```

```bash
helm upgrade -i flux fluxcd/flux --wait \
--namespace flux-demo \
--set git.url=git@github.com:scharret/git-ops-demo \
--set sync.interval=30s \
--set git.branch=main \
--set git.path=applications
```

```bash
kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/master/deploy/crds.yaml
```

```bash
helm upgrade -i helm-operator fluxcd/helm-operator --wait \
--namespace flux-demo \
--set git.ssh.secretName=flux-git-deploy \
--set helm.versions=v3
```

# Get Deploy Key And add it as deploy key in Github repository

```bash
fluxctl identity --k8s-fwd-ns fluxcd > clipcopy
```
