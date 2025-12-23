# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Kubernetes homelab applications repository that manages infrastructure components using Helm charts. Each application has its own directory containing Helm values files and deployment instructions.

## Repository Structure

The repository follows a consistent pattern for each application:
- `<app>/values.yaml` - Default values from the upstream Helm chart (reference only)
- `<app>/own-values.yaml` - Custom values that override defaults
- `<app>/Readme.md` - Deployment commands and version information

Key applications include:
- **Loki** - Log aggregation system with S3 storage backend
- **Alloy** - Log collection agent (replaces Promtail) with custom config.alloy
- **kube-prometheus-stack** - Monitoring stack (Prometheus, Grafana, Alertmanager)
- **Cilium** - CNI and network policy enforcement (deployed to kube-system)
- **Trident** - NetApp storage orchestrator
- **ingress-nginx** - Ingress controller
- **ArgoCD** - GitOps continuous delivery
- **Redis Operator** - Redis deployment management with multiple deployment modes
- **Kyverno** - Kubernetes policy engine
- **GitLab Runner** - CI/CD runner

## Helm Workflow

### Getting Default Values
```bash
helm show values <repo>/<chart> --version <version> > <app>/values.yaml
```

### Installing/Upgrading Applications
Standard pattern:
```bash
helm repo add <repo-name> <repo-url>
helm repo update
helm upgrade --install <release-name> <repo>/<chart> \
  --version <version> \
  --namespace <namespace> \
  --create-namespace \
  -f <app>/own-values.yaml \
  --wait
```

### Important Notes
- Always use `helm upgrade --install` for idempotent deployments
- Versions are pinned in each application's Readme.md
- Custom configurations go in `own-values.yaml`, never modify `values.yaml`
- Use `--wait` flag to ensure deployments complete before returning

## Application-Specific Details

### Alloy Configuration
Alloy uses an external ConfigMap for configuration:
```bash
# ConfigMap must be created separately before Helm install
kubectl delete configmap --namespace alloy alloy-config
kubectl create configmap --namespace alloy alloy-config "--from-file=config.alloy=alloy/config.alloy"
helm upgrade --install alloy grafana/alloy --version 0.12.5 -f alloy/own-values.yaml --namespace alloy --create-namespace --wait
```

The `config.alloy` file contains Alloy's pipeline configuration for:
- Kubernetes pod discovery
- Log relabeling and filtering (currently targets ingress-nginx)
- JSON parsing and field extraction
- Loki write endpoint with authentication

Key configuration points in `own-values.yaml`:
- `alloy.configMap.create: false` - Uses pre-created ConfigMap
- `controller.type: daemonset` - Runs on every node

### Loki Setup
Loki requires external authentication setup:
```bash
# Create htpasswd file first
htpasswd -c auth user_name
htpasswd auth add_user_name

# Create secret from auth file
kubectl create secret generic loki-basic-auth \
  --from-file=loki/auth \
  --namespace=loki
```

Architecture:
- Deployment mode: `SimpleScalable` (read, write, backend components)
- Storage: S3 backend (bucketNames.chunks configuration required)
- Ingress: nginx with basic auth annotation
- Retention: 120h (5 days) with compaction enabled
- Schema: TSDB v13 with 24h index period

### Redis Operator
The Redis Operator supports multiple deployment patterns in the same namespace (`ot-operators`):
1. **Operator** - Core operator deployment
2. **Redis Replication** - Master-replica setup
3. **Redis Sentinel** - High availability with sentinel
4. **Redis Cluster** - Distributed cluster mode
5. **Redis Standalone** - Single instance

Each mode has separate values files: `redis-<mode>-values.yaml` and `redis-<mode>-values-own.yaml`

### Cilium
Cilium is deployed to the `kube-system` namespace (not a dedicated namespace) as it provides core CNI functionality.

### Monitoring Stack (kube-prometheus-stack)
The release name is `monitoring` (not `kube-prometheus-stack`). This affects how resources are labeled and named in the cluster.

## Git Workflow

The repository tracks:
- Custom values files (`own-values.yaml`)
- Application-specific configs (e.g., `alloy/config.alloy`)
- Secrets tracking (e.g., `loki/auth` file is tracked)

**Warning**: Some files in this repository contain sensitive credentials (S3 keys, passwords, API tokens) in plain text. These should be removed from git history and migrated to Kubernetes secrets or a secrets management solution.

## Common Operations

### Updating an Application Version
1. Check the application's Readme.md for current version
2. Update the version in Readme.md
3. Fetch new default values: `helm show values <repo>/<chart> --version <new-version> > <app>/values.yaml`
4. Review changes in `values.yaml` and update `own-values.yaml` if needed
5. Run the upgrade command from the Readme.md with new version

### Adding a New Application
1. Create directory: `mkdir <app>`
2. Add Helm repo and fetch values
3. Create `own-values.yaml` with customizations
4. Document installation commands in `Readme.md` including version
5. Test installation in a dev environment first

### Troubleshooting Deployments
```bash
# Check Helm release status
helm list -A

# View release history
helm history <release-name> -n <namespace>

# Check pod status
kubectl get pods -n <namespace>

# View pod logs
kubectl logs -n <namespace> <pod-name>

# Describe resources for events
kubectl describe pod -n <namespace> <pod-name>
```
