Version: 0.12.5
helm show values grafana/alloy --version 0.12.5 > alloy/values.yaml

# Add Repo
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# Install
kubectl delete configmap --namespace alloy alloy-config
kubectl create configmap --namespace alloy alloy-config "--from-file=config.alloy=alloy/config.alloy"
helm upgrade --install alloy grafana/alloy --version 0.12.5 --namespace alloy --create-namespace -f alloy/own-values.yaml --wait