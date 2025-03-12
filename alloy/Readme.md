Version: 0.12.1
helm show values grafana/alloy --version 0.12.1 > alloy/values.yaml

# Add Repo
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# Install
kubectl create configmap --namespace alloy alloy-config "--from-file=config.alloy=alloy/config.alloy"
helm upgrade --install alloy grafana/alloy --version 0.12.1 --namespace alloy --create-namespace -f alloy/own-values.yaml --wait