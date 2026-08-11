# Helm CLI - Cheatsheet

**Last Updated: 2026-08**

Helm is the package manager for Kubernetes. It uses **charts** to define, install, upgrade, and manage Kubernetes applications.

---

## Basic Syntax

```bash
helm [command] [flags]
```

Common examples:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm search repo nginx
helm install my-app bitnami/nginx
helm list
helm upgrade my-app bitnami/nginx
```

---

## Helm Concepts

| Concept | Description |
|---|---|
| **Chart** | A package containing Kubernetes resource definitions |
| **Release** | A deployed instance of a chart |
| **Repository** | A location containing packaged Helm charts |
| **Values** | Configuration used to customize a chart |
| **Revision** | A version of a release's deployment history |
| **Template** | Kubernetes YAML generated from a chart and its values |

Typical workflow:

```text
Repository → Chart → Install → Release
                         ↑
                       Values
```

---

## Version and Environment

```bash
# Helm version
helm version

# Helm environment information
helm env

# Show Helm help
helm help

# Help for a specific command
helm help install
helm install --help
```

---

## Repositories

### Add a Repository

```bash
helm repo add <name> <url>
```

Example:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

### List Repositories

```bash
helm repo list
```

### Update Repository Indexes

```bash
helm repo update
```

### Remove a Repository

```bash
helm repo remove <name>
```

### Search Repositories

```bash
# Search configured repositories
helm search repo <keyword>

# Search for an exact chart
helm search repo bitnami/nginx

# Search Artifact Hub
helm search hub <keyword>
```

---

## Charts

### Show Chart Information

```bash
helm show chart <chart>
```

### Show Chart Values

```bash
helm show values <chart>
```

### Show Chart README

```bash
helm show readme <chart>
```

### Show All Chart Information

```bash
helm show all <chart>
```

Example:

```bash
helm show values bitnami/nginx
```

---

## Install a Chart

```bash
helm install <release> <chart>
```

Example:

```bash
helm install my-nginx bitnami/nginx
```

### Specify a Namespace

```bash
helm install my-nginx bitnami/nginx \
  --namespace web \
  --create-namespace
```

### Set Values

```bash
helm install my-nginx bitnami/nginx \
  --set service.type=NodePort
```

Multiple values:

```bash
helm install my-nginx bitnami/nginx \
  --set service.type=NodePort \
  --set replicaCount=2
```

### Use a Values File

```bash
helm install my-nginx bitnami/nginx \
  -f values.yaml
```

Multiple values files:

```bash
helm install my-nginx bitnami/nginx \
  -f values.yaml \
  -f values-production.yaml
```

Later files override earlier files.

### Install a Specific Chart Version

```bash
helm install my-nginx bitnami/nginx \
  --version <version>
```

### Generate a Name Automatically

```bash
helm install bitnami/nginx \
  --generate-name
```

---

## Dry Run

Preview what Helm would install without actually installing it:

```bash
helm install my-app ./my-chart \
  --dry-run
```

Include debug information:

```bash
helm install my-app ./my-chart \
  --dry-run \
  --debug
```

This is extremely useful for troubleshooting templates and values.

---

## Template Rendering

Render a chart locally without contacting the Kubernetes API:

```bash
helm template my-app ./my-chart
```

With a values file:

```bash
helm template my-app ./my-chart \
  -f values.yaml
```

With custom values:

```bash
helm template my-app ./my-chart \
  --set image.tag=v2
```

Save rendered manifests:

```bash
helm template my-app ./my-chart \
  > rendered.yaml
```

Validate rendered manifests:

```bash
helm template my-app ./my-chart \
  | kubectl apply --dry-run=server -f -
```

---

## List Releases

```bash
# Releases in current namespace
helm list

# Releases in a specific namespace
helm list -n <namespace>

# Releases across all namespaces
helm list -A

# Include releases in all states
helm list --all

# Show only deployed releases
helm list --deployed

# Show failed releases
helm list --failed
```

Useful output:

```bash
helm list -A
```

Typical columns include:

```text
NAME    NAMESPACE    REVISION    UPDATED    STATUS    CHART    APP VERSION
```

---

## Release Status

```bash
helm status <release>
```

Specific namespace:

```bash
helm status <release> -n <namespace>
```

Show additional information:

```bash
helm status <release> --show-resources
```

---

## Release History

```bash
helm history <release>
```

Specific namespace:

```bash
helm history <release> -n <namespace>
```

Example:

```text
REVISION    STATUS
1           superseded
2           superseded
3           deployed
```

---

## Upgrade a Release

```bash
helm upgrade <release> <chart>
```

Example:

```bash
helm upgrade my-nginx bitnami/nginx
```

### Upgrade with Values

```bash
helm upgrade my-nginx bitnami/nginx \
  -f values.yaml
```

### Upgrade with `--set`

```bash
helm upgrade my-nginx bitnami/nginx \
  --set replicaCount=3
```

### Install if Release Does Not Exist

```bash
helm upgrade --install my-app ./my-chart
```

This is one of the most useful Helm commands for automation and CI/CD.

### Reuse Existing Values

```bash
helm upgrade my-app ./my-chart \
  --reuse-values
```

### Wait for Resources

```bash
helm upgrade my-app ./my-chart \
  --wait
```

Wait with a timeout:

```bash
helm upgrade my-app ./my-chart \
  --wait \
  --timeout 10m
```

---

## Roll Back a Release

### View History

```bash
helm history my-app
```

### Roll Back

```bash
helm rollback my-app <revision>
```

Example:

```bash
helm rollback my-app 2
```

### Roll Back and Wait

```bash
helm rollback my-app 2 \
  --wait
```

Verify:

```bash
helm status my-app
helm history my-app
```

---

## Uninstall a Release

```bash
helm uninstall <release>
```

Specific namespace:

```bash
helm uninstall <release> \
  --namespace <namespace>
```

Keep release history:

```bash
helm uninstall <release> \
  --keep-history
```

List deleted releases retained in history:

```bash
helm list --all
```

---

## Inspect a Release

### Get Values

```bash
helm get values <release>
```

Show all values, including chart defaults:

```bash
helm get values <release> \
  --all
```

### Get Manifest

```bash
helm get manifest <release>
```

### Get Notes

```bash
helm get notes <release>
```

### Get Hooks

```bash
helm get hooks <release>
```

### Get Everything

```bash
helm get all <release>
```

---

## Chart Dependencies

### Update Dependencies

```bash
helm dependency update ./my-chart
```

### Build Dependencies from Lock File

```bash
helm dependency build ./my-chart
```

### List Dependencies

```bash
helm dependency list ./my-chart
```

### Example

```bash
helm dependency update ./my-chart
helm template my-app ./my-chart
```

---

## Create a New Chart

```bash
helm create my-chart
```

Generated structure:

```text
my-chart/
  - Chart.yaml
  - values.yaml
  - charts/
  - templates/
      - deployment.yaml
      - service.yaml
      - ingress.yaml
      - serviceaccount.yaml
      - _helpers.tpl
      - NOTES.txt
  - .helmignore
```

---

## Chart.yaml

Typical chart metadata:

```yaml
apiVersion: v2
name: my-chart
description: My Kubernetes application
type: application
version: 0.1.0
appVersion: "1.0.0"
```

| Field | Description |
|---|---|
| `apiVersion` | Helm chart API version |
| `name` | Chart name |
| `description` | Chart description |
| `type` | `application` or `library` |
| `version` | Chart version |
| `appVersion` | Version of the application |

---

## Package a Chart

```bash
helm package ./my-chart
```

Specify output directory:

```bash
helm package ./my-chart \
  --destination ./packages
```

The result is typically:

```text
my-chart-0.1.0.tgz
```

---

## Lint a Chart

Check a chart for common problems:

```bash
helm lint ./my-chart
```

With values:

```bash
helm lint ./my-chart \
  -f values.yaml
```

---

## Pull a Chart

Download a chart without installing it:

```bash
helm pull bitnami/nginx
```

Download and extract:

```bash
helm pull bitnami/nginx \
  --untar
```

Specify a version:

```bash
helm pull bitnami/nginx \
  --version <version> \
  --untar
```

---

## Chart Repositories and OCI Registries

Helm supports OCI-based registries.

### Login

```bash
helm registry login <registry>
```

Example:

```bash
helm registry login registry.example.com
```

### Pull an OCI Chart

```bash
helm pull oci://registry.example.com/charts/my-chart
```

### Install an OCI Chart

```bash
helm install my-app \
  oci://registry.example.com/charts/my-chart
```

### Push a Chart

Package first:

```bash
helm package ./my-chart
```

Then:

```bash
helm push my-chart-0.1.0.tgz \
  oci://registry.example.com/charts
```

---

## Values

Values customize a chart.

### View Default Values

```bash
helm show values <chart>
```

### Override with `--set`

```bash
helm install my-app ./my-chart \
  --set replicaCount=3
```

Nested values:

```bash
helm install my-app ./my-chart \
  --set image.repository=myapp \
  --set image.tag=v2
```

### Override with a File

```yaml
# production.yaml

replicaCount: 3

image:
  repository: myapp
  tag: v2

service:
  type: LoadBalancer
```

Install:

```bash
helm install my-app ./my-chart \
  -f production.yaml
```

---

## Value Precedence

When multiple sources define the same value, later/higher-precedence values win.

A common order is:

```text
Chart defaults
    ↓
values.yaml
    ↓
additional -f values files
    ↓
--set / --set-string / --set-file
```

Example:

```bash
helm upgrade my-app ./my-chart \
  -f values.yaml \
  -f production.yaml \
  --set image.tag=v3
```

Here `image.tag=v3` takes precedence over the values files.

---

## Common `--set` Options

```bash
# String/number/boolean values
--set replicaCount=3

# Force value to remain a string
--set-string image.tag=001

# Read value from a file
--set-file config=./config.json

# Reset values to chart defaults during upgrade
--reset-values

# Reuse values from previous release
--reuse-values
```

---

## Helm Templates

### Render Values

```yaml
replicas: {{ .Values.replicaCount }}
```

### Access Nested Values

```yaml
image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

### Conditional

```yaml
{{- if .Values.ingress.enabled }}
apiVersion: networking.k8s.io/v1
kind: Ingress
...
{{- end }}
```

### Loop

```yaml
{{- range .Values.hosts }}
- {{ . }}
{{- end }}
```

### Default Value

```yaml
replicas: {{ .Values.replicaCount | default 1 }}
```

### Quote a Value

```yaml
name: {{ .Values.name | quote }}
```

### Include a Named Template

```yaml
{{ include "my-chart.fullname" . }}
```

---

## Helm Template Debugging

Render with debug output:

```bash
helm template my-app ./my-chart \
  --debug
```

Dry-run an install:

```bash
helm install my-app ./my-chart \
  --dry-run \
  --debug
```

Validate generated Kubernetes manifests:

```bash
helm template my-app ./my-chart \
  | kubectl apply --dry-run=server -f -
```

Check chart syntax:

```bash
helm lint ./my-chart
```

---

## Namespace Management

```bash
# Install into namespace
helm install my-app ./my-chart \
  --namespace my-namespace \
  --create-namespace

# List releases in namespace
helm list -n my-namespace

# Upgrade in namespace
helm upgrade my-app ./my-chart \
  -n my-namespace

# Uninstall from namespace
helm uninstall my-app \
  -n my-namespace
```

---

## Hooks

Helm hooks allow resources to run at specific points in a release lifecycle.

Common hooks:

```yaml
annotations:
  "helm.sh/hook": pre-install
```

Other lifecycle hooks include:

```text
pre-install
post-install
pre-delete
post-delete
pre-upgrade
post-upgrade
pre-rollback
post-rollback
test
```

View hooks for a release:

```bash
helm get hooks <release>
```

---

## Helm Tests

If a chart defines Helm tests:

```bash
helm test <release>
```

List test resources:

```bash
kubectl get pods \
  -l "helm.sh/hook=test"
```

---

## Kubernetes Integration

Helm creates Kubernetes resources, so `kubectl` is often used alongside Helm.

```bash
# See Helm releases
helm list

# See Kubernetes resources
kubectl get all

# Inspect Helm-created pods
kubectl get pods

# Inspect a Helm-created deployment
kubectl describe deployment <deployment>

# View application logs
kubectl logs <pod>

# Check Helm release status
helm status <release>
```

---

## Troubleshooting a Release

A useful troubleshooting sequence:

```bash
# 1. Check release status
helm status <release>

# 2. Check release history
helm history <release>

# 3. Inspect configured values
helm get values <release> --all

# 4. Inspect rendered Kubernetes manifests
helm get manifest <release>

# 5. Check Kubernetes resources
kubectl get all

# 6. Check pods
kubectl get pods

# 7. Describe failing pods
kubectl describe pod <pod>

# 8. Check logs
kubectl logs <pod>

# 9. Check Kubernetes events
kubectl get events --sort-by='.lastTimestamp'
```

---

## Common Helm Release States

| Status | Meaning |
|---|---|
| `deployed` | Release successfully deployed |
| `failed` | Installation/upgrade failed |
| `pending-install` | Installation is in progress |
| `pending-upgrade` | Upgrade is in progress |
| `pending-rollback` | Rollback is in progress |
| `uninstalling` | Release is being removed |
| `uninstalled` | Release was uninstalled |
| `superseded` | Release revision was replaced |

---

## Useful Flags

| Flag | Description |
|---|---|
| `-n, --namespace` | Kubernetes namespace |
| `-f, --values` | Specify values file |
| `--set` | Set a value |
| `--set-string` | Set a value as a string |
| `--set-file` | Set a value from a file |
| `--version` | Specify chart version |
| `--dry-run` | Simulate an operation |
| `--debug` | Enable debug output |
| `--wait` | Wait for resources to become ready |
| `--timeout` | Set operation timeout |
| `--create-namespace` | Create namespace if needed |
| `--atomic` | Roll back if operation fails |
| `--reuse-values` | Reuse previous release values |
| `--reset-values` | Reset to chart defaults |
| `--dependency-update` | Update dependencies before operation |

### Atomic Deployments

Useful in CI/CD:

```bash
helm upgrade --install my-app ./my-chart \
  --atomic \
  --wait \
  --timeout 10m
```

If the operation fails, `--atomic` rolls the release back.

---

## Common Workflows

### Install an Application

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update

helm install my-app bitnami/nginx \
  --namespace web \
  --create-namespace
```

### Install with Custom Values

```bash
helm install my-app ./my-chart \
  --namespace production \
  --create-namespace \
  -f production.yaml
```

### Deploy or Upgrade

```bash
helm upgrade --install my-app ./my-chart \
  --namespace production \
  --create-namespace \
  -f production.yaml \
  --wait
```

### Safe CI/CD Deployment

```bash
helm upgrade --install my-app ./my-chart \
  --namespace production \
  --create-namespace \
  -f production.yaml \
  --atomic \
  --wait \
  --timeout 10m
```

### Roll Back

```bash
helm history my-app

helm rollback my-app 2 \
  --wait
```

### Remove an Application

```bash
helm uninstall my-app \
  --namespace production
```

---

## Quick Cheat Sheet

| Task | Command |
|---|---|
| Helm version | `helm version` |
| Add repository | `helm repo add <name> <url>` |
| Update repositories | `helm repo update` |
| List repositories | `helm repo list` |
| Search charts | `helm search repo <keyword>` |
| Show chart values | `helm show values <chart>` |
| Show chart info | `helm show chart <chart>` |
| Install chart | `helm install <release> <chart>` |
| Install with values | `helm install <release> <chart> -f values.yaml` |
| Install/upgrade | `helm upgrade --install <release> <chart>` |
| Dry run | `helm install <release> <chart> --dry-run --debug` |
| Render templates | `helm template <release> <chart>` |
| List releases | `helm list` |
| List all releases | `helm list -A` |
| Release status | `helm status <release>` |
| Release history | `helm history <release>` |
| Upgrade release | `helm upgrade <release> <chart>` |
| Roll back | `helm rollback <release> <revision>` |
| Get values | `helm get values <release>` |
| Get all release info | `helm get all <release>` |
| Get manifests | `helm get manifest <release>` |
| Uninstall | `helm uninstall <release>` |
| Create chart | `helm create <chart>` |
| Lint chart | `helm lint <chart>` |
| Package chart | `helm package <chart>` |
| Pull chart | `helm pull <chart>` |
| Update dependencies | `helm dependency update <chart>` |
| Run chart tests | `helm test <release>` |
| Registry login | `helm registry login <registry>` |

---

## Helm + kubectl Cheat Sheet

```bash
# Helm
helm list -A
helm status <release>
helm history <release>
helm get values <release> --all
helm get manifest <release>

# Kubernetes
kubectl get pods
kubectl get deployments
kubectl get services
kubectl describe pod <pod>
kubectl logs <pod>
kubectl get events --sort-by='.lastTimestamp'
```

A typical production troubleshooting flow:

```text
helm status
    ↓
helm history
    ↓
helm get values
    ↓
helm get manifest
    ↓
kubectl get pods
    ↓
kubectl describe pod
    ↓
kubectl logs
    ↓
kubectl get events
```
