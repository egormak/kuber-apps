# Get Values
Version: 0.49.0
helm show values vector/vector --version 0.49.0 > vector/values.yaml

# Add Repo
helm repo add vector https://helm.vector.dev
helm repo update

# OpenShift: Grant logging-scc to Vector ServiceAccount
# This must be done BEFORE installing Vector
oc adm policy add-scc-to-user logging-scc -z vector -n vector

# Install
helm upgrade --install vector vector/vector --version 0.49.0 --namespace vector --create-namespace -f vector/own-values.yaml --wait

# Configuration Notes

## Role: Agent (DaemonSet)
Vector is configured to run as an Agent (DaemonSet) to collect logs and metrics from all nodes in the cluster.

## Customization
The `own-values.yaml` includes a basic configuration that:
- Collects Kubernetes logs from all pods
- Parses and enriches log data with Kubernetes metadata
- Outputs to console (for testing) - **configure appropriate sinks for production**

## Integration with Loki
To send logs to Loki, uncomment the `loki` sink in `own-values.yaml` and configure the endpoint:
```yaml
sinks:
  loki:
    type: loki
    inputs:
      - remap_kubernetes
    endpoint: http://loki.loki.svc.cluster.local:3100
    encoding:
      codec: json
    labels:
      namespace: "{{ namespace }}"
      pod: "{{ pod }}"
      container: "{{ application }}"
```

## Monitoring
Enable ServiceMonitor or PodMonitor in `own-values.yaml` to integrate with Prometheus:
```yaml
serviceMonitor:
  enabled: true
  additionalLabels:
    release: monitoring
```

## Resources
Adjust resource requests and limits based on your cluster capacity and log volume in `own-values.yaml`.
