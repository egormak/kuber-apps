Version: 2.7.2
helm show values kubecost/cost-analyzer --version 2.7.2 > kubecost-openshift/values.yaml

# Before Install
oc label namespace kubecost openshift.io/cluster-monitoring=true

# Add Repo
helm repo add kubecost https://kubecost.github.io/cost-analyzer/
helm repo update

# Install
helm upgrade --install kubecost kubecost/cost-analyzer --version 2.7.2 --namespace kubecost --create-namespace -f kubecost-openshift/own-values.yaml --wait
