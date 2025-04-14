Version: 100.2502.1
helm show values netapp-trident/trident-operator --version 100.2502.1 > trident/values.yaml

# Add Repo
helm repo add netapp-trident https://netapp.github.io/trident-helm-chart
helm repo update

# Install 
helm upgrade --install trident-operator netapp-trident/trident-operator --version 100.2502.1 --namespace trident-operator --create-namespace -f trident/own-values.yaml --wait