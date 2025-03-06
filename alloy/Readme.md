Version: 0.12.1
helm show values grafana/alloy --version 0.12.1 > alloy/values.yaml

# Add Repo
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update