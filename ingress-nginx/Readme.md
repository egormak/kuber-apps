# Get Values
Version: 4.12.0
helm show values ingress-nginx/ingress-nginx --version 4.12.0 > ingress-nginx/values.yaml

# Add Repo
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

# Install
helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx --version 4.12.0 --namespace ingress-nginx --create-namespace -f ingress-nginx/own-values.yaml --wait