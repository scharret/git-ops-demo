# Create Namespace

kubectl create ns flux-demo

kubectl create ns app

# Install Flux & HelmRelease
helm repo add fluxcd https://charts.fluxcd.io

helm upgrade -i flux fluxcd/flux --wait \
--namespace flux-demo \
--set git.url=git@github.com:scharret/git-ops-demo \
--set sync.interval=30s \
--set git.branch=main \
--set git.path=applications

kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/master/deploy/crds.yaml

helm upgrade -i helm-operator fluxcd/helm-operator --wait \
--namespace flux-demo \
--set git.ssh.secretName=flux-git-deploy \
--set helm.versions=v3


# Get Deploy Key And add it as deploy key in Github repository
fluxctl identity --k8s-fwd-ns fluxcd > clipcopy
