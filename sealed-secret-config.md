# Create Namespace

kubectl create ns admin

# Install Sealed secret
helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
helm install --namespace admin sealed-secrets sealed-secrets/sealed-secrets

# Secret Resource Creation
apiVersion: v1
data:
  myapp: dG90bw==
kind: Secret
metadata:
  name: secret-myapp
  namespace: flux-demo
type: Opaque

# Installation of Kubeseal
## Linux:
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.16.0/kubeseal-linux-amd64 -O kubeseal
sudo install -m 755 kubeseal /usr/local/bin/kubeseal
## Mac:
brew install kubeseal

# Apply the sealed secret on the Resource Secret
kubeseal --scope namespace-wide --controller-namespace admin --controller-name sealed-secrets -o yaml < secret.yml

# Check CRD
kubectl get sealedsecret -n flux-demo -o yaml secret-myapp

# Check du Secret
kubectl view-secret -n flux-demo secret-myapp
