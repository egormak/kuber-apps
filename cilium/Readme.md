# Get Values
Version: 1.17.1
helm show values cilium/cilium --version 1.17.1 > cilium/values.yaml

# Add Repo
helm repo add cilium https://helm.cilium.io/
helm repo update

# Install
helm upgrade --install cilium cilium/cilium --version 1.17.1 --namespace kube-system -f cilium/own-values.yaml --wait