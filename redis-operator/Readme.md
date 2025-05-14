Version: 0.20.1
helm show values ot-helm/redis-operator --version 0.20.1 > redis-operator/values.yaml

# Install Redis Operator
```bash
helm repo add ot-helm https://ot-container-kit.github.io/helm-charts/
helm upgrade --install redis-operator ot-helm/redis-operator --namespace ot-operators --version 0.20.1 --create-namespace --wait
```
# Install Redis Replication
Version: 0.16.7
```bash
helm show values ot-helm/redis-replication --version 0.16.7 > redis-operator/redis-replication-values.yaml
helm upgrade --install redis-replication ot-helm/redis-replication --version 0.16.7 --namespace ot-operators -f redis-operator/redis-replication-values-own.yaml --wait
```
# Install Redis Sentinel
Version: 0.16.9
```bash
helm show values ot-helm/redis-sentinel --version 0.16.9 > redis-operator/redis-sentinel-values.yaml
helm upgrade --install redis-sentinel ot-helm/redis-sentinel --namespace ot-operators --version 0.16.9 -f redis-operator/redis-sentinel-values-own.yaml --wait
```
# Install Redis Cluster
Version: 0.16.5
```bash
helm show values ot-helm/redis-cluster --version 0.16.5 > redis-operator/redis-cluster-values.yaml
helm upgrade --install redis-cluster ot-helm/redis-cluster --namespace ot-operators --version 0.16.5 -f redis-operator/redis-cluster-values-own.yaml --wait
```
# Install Redis Standalone
helm repo add ot-helm https://ot-container-kit.github.io/helm-charts/
helm install redis-standalone ot-helm/redis-standalone --namespace ot-operators
```