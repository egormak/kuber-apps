Version: 6.27.0
helm show values grafana/loki --version 6.27.0 > loki/values.yaml

# Add Repo
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# Install
helm upgrade --install loki grafana/loki --version 6.27.0 --namespace loki --create-namespace -f loki/own-values.yaml --wait