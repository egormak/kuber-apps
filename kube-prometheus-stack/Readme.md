Version: 70.0.0
helm show values prometheus-community/kube-prometheus-stack --version 70.0.0 > kube-prometheus-stack/values.yaml

# Add Repo
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Install
helm upgrade --install monitoring prometheus-community/kube-prometheus-stack --version 70.0.0 --namespace monitoring --create-namespace -f kube-prometheus-stack/own-values.yaml --wait
