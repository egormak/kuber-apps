Version: 6.29.0
helm show values grafana/loki --version 6.29.0 > loki/values.yaml

# Add Repo
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

# Install
helm upgrade --install loki grafana/loki --version 6.29.0 --namespace loki --create-namespace -f loki/own-values.yaml --wait

# Secure
kubectl create secret generic loki-basic-auth \
  --from-file=loki/auth \
  --namespace=loki

# Htpasswd
htpasswd -c auth user_name
htpasswd auth add_user_name
