# Git CLI - Cheatsheet

**Last Updated: 2026-08

# Kubernetes CLI (`kubectl`) Quick Reference

`kubectl` is the primary command-line tool for interacting with Kubernetes clusters.

---

## Basic Syntax

```bash
kubectl [command] [TYPE] [NAME] [flags]
```

Examples:

```bash
kubectl get pods
kubectl describe pod my-pod
kubectl delete deployment my-app
kubectl logs my-pod
```

Useful shorthand resource names:

| Resource | Short Name |
|---|---|
| `pods` | `po` |
| `services` | `svc` |
| `deployments` | `deploy` |
| `replicasets` | `rs` |
| `statefulsets` | `sts` |
| `daemonsets` | `ds` |
| `configmaps` | `cm` |
| `secrets` | `secret` |
| `namespaces` | `ns` |
| `persistentvolumes` | `pv` |
| `persistentvolumeclaims` | `pvc` |
| `serviceaccounts` | `sa` |
| `nodes` | `no` |

---

## Cluster Information

```bash
# Display cluster information
kubectl cluster-info

# Display Kubernetes client and server versions
kubectl version

# Display API resources supported by the cluster
kubectl api-resources

# Display API versions
kubectl api-versions

# Get cluster component information
kubectl get componentstatuses
```

> `componentstatuses` is deprecated on many modern Kubernetes versions and may not be available.

---

## Contexts and Configuration

Contexts determine which cluster, user, and namespace `kubectl` uses.

```bash
# Display current context
kubectl config current-context

# List available contexts
kubectl config get-contexts

# Switch context
kubectl config use-context <context>

# Display kubeconfig
kubectl config view

# Display current-context details
kubectl config view --minify

# Set default namespace for current context
kubectl config set-context --current --namespace=<namespace>
```

### Useful Environment Variable

```bash
# Use a specific kubeconfig file
export KUBECONFIG=~/.kube/config
```

Multiple kubeconfig files can be specified:

```bash
export KUBECONFIG=~/.kube/config:~/.kube/config-dev
```

---

## Namespaces

```bash
# List namespaces
kubectl get namespaces

# Create a namespace
kubectl create namespace <namespace>

# Describe a namespace
kubectl describe namespace <namespace>

# Delete a namespace
kubectl delete namespace <namespace>

# Run a command in a specific namespace
kubectl get pods -n <namespace>

# Run a command across all namespaces
kubectl get pods --all-namespaces
```

---

## Get Resources

The `get` command is one of the most commonly used `kubectl` commands.

```bash
# List pods
kubectl get pods

# List pods with additional information
kubectl get pods -o wide

# List all pods in all namespaces
kubectl get pods -A

# List deployments
kubectl get deployments

# List services
kubectl get services

# List nodes
kubectl get nodes

# List all common resources
kubectl get all

# Get a specific resource
kubectl get pod <pod>

# Get YAML definition
kubectl get pod <pod> -o yaml

# Get JSON definition
kubectl get pod <pod> -o json
```

### Labels

```bash
# Display labels
kubectl get pods --show-labels

# Select resources by label
kubectl get pods -l app=myapp

# Multiple label requirements
kubectl get pods -l 'app=myapp,environment=prod'
```

---

## Describe Resources

`describe` provides detailed human-readable information and events.

```bash
kubectl describe pod <pod>
kubectl describe deployment <deployment>
kubectl describe service <service>
kubectl describe node <node>
kubectl describe namespace <namespace>
```

For troubleshooting, start with:

```bash
kubectl describe pod <pod>
```

Then check the **Events** section near the bottom.

---

## Pods

### Create a Pod

```bash
kubectl run nginx --image=nginx
```

### List Pods

```bash
kubectl get pods
kubectl get pods -o wide
kubectl get pods -A
```

### Delete a Pod

```bash
kubectl delete pod <pod>
```

### Get Pod YAML

```bash
kubectl get pod <pod> -o yaml
```

### Watch Pods

```bash
kubectl get pods --watch
```

### Filter by Status

```bash
kubectl get pods --field-selector=status.phase=Running
```

---

## Deployments

### Create a Deployment

```bash
kubectl create deployment myapp --image=myimage:latest
```

### List Deployments

```bash
kubectl get deployments
```

### Scale a Deployment

```bash
kubectl scale deployment myapp --replicas=3
```

### Update an Image

```bash
kubectl set image deployment/myapp mycontainer=myimage:v2
```

### Get Deployment Status

```bash
kubectl rollout status deployment/myapp
```

### View Rollout History

```bash
kubectl rollout history deployment/myapp
```

### Roll Back

```bash
kubectl rollout undo deployment/myapp
```

### Roll Back to a Specific Revision

```bash
kubectl rollout undo deployment/myapp --to-revision=2
```

### Restart a Deployment

```bash
kubectl rollout restart deployment/myapp
```

---

## Services

```bash
# List services
kubectl get services

# Describe a service
kubectl describe service <service>

# Expose a deployment
kubectl expose deployment myapp --port=80 --target-port=8080

# Expose a deployment as NodePort
kubectl expose deployment myapp --type=NodePort --port=80

# Delete a service
kubectl delete service <service>
```

### Service Types

| Type | Purpose |
|---|---|
| `ClusterIP` | Internal cluster access; default |
| `NodePort` | Exposes service on each node |
| `LoadBalancer` | Requests an external load balancer |
| `ExternalName` | Maps service to an external DNS name |

---

## Port Forwarding

Useful for accessing a service or pod locally without exposing it externally.

```bash
# Forward local port to a pod
kubectl port-forward pod/<pod> 8080:80

# Forward local port to a service
kubectl port-forward service/<service> 8080:80

# Forward to a deployment
kubectl port-forward deployment/<deployment> 8080:80
```

Then access:

```text
http://localhost:8080
```

---

## Logs

```bash
# View pod logs
kubectl logs <pod>

# Follow logs
kubectl logs -f <pod>

# Show previous container logs
kubectl logs <pod> --previous

# Show last 100 lines
kubectl logs --tail=100 <pod>

# Show timestamps
kubectl logs --timestamps <pod>

# Logs from a specific container
kubectl logs <pod> -c <container>

# Follow logs from a deployment
kubectl logs -f deployment/<deployment>
```

---

## Execute Commands in Containers

```bash
# Open a shell
kubectl exec -it <pod> -- /bin/bash

# If bash isn't available
kubectl exec -it <pod> -- /bin/sh

# Execute a command
kubectl exec <pod> -- ls -la

# Execute in a specific container
kubectl exec -it <pod> -c <container> -- /bin/sh
```

---

## Copy Files

```bash
# Pod-> local machine
kubectl cp <pod>:/path/to/file ./file

# Local machine-> pod
kubectl cp ./file <pod>:/path/to/file

# Specific namespace
kubectl cp <namespace>/<pod>:/path/to/file ./file
```

For multi-container pods:

```bash
kubectl cp <pod>:/path/to/file ./file -c <container>
```

---

## ConfigMaps

```bash
# List ConfigMaps
kubectl get configmaps

# Create from a literal value
kubectl create configmap app-config \
  --from-literal=environment=production

# Create from a file
kubectl create configmap app-config \
  --from-file=config.properties

# View ConfigMap
kubectl get configmap app-config -o yaml

# Describe ConfigMap
kubectl describe configmap app-config

# Delete ConfigMap
kubectl delete configmap app-config
```

---

## Secrets

```bash
# List secrets
kubectl get secrets

# Create a generic secret
kubectl create secret generic app-secret \
  --from-literal=username=admin \
  --from-literal=password=secret

# View secret metadata
kubectl describe secret app-secret

# View encoded secret data
kubectl get secret app-secret -o yaml
```

Decode a value:

```bash
kubectl get secret app-secret \
  -o jsonpath='{.data.password}' | base64 --decode
```

> Kubernetes Secret values are Base64-encoded by default; Base64 encoding is **not encryption**.

---

## Apply / Create / Delete

### Apply YAML

```bash
# Create or update resources
kubectl apply -f deployment.yaml

# Apply an entire directory
kubectl apply -f ./k8s/

# Apply recursively
kubectl apply -R -f ./k8s/
```

### Create

```bash
kubectl create -f deployment.yaml
```

### Delete

```bash
# Delete a resource
kubectl delete pod <pod>

# Delete using a manifest
kubectl delete -f deployment.yaml

# Delete all pods matching a label
kubectl delete pods -l app=myapp
```

> `kubectl apply` is generally preferred for declarative configuration.

---

## YAML Generation

Generate YAML without actually creating the resource:

```bash
kubectl create deployment myapp \
  --image=nginx \
  --dry-run=client \
  -o yaml
```

Save it to a file:

```bash
kubectl create deployment myapp \
  --image=nginx \
  --dry-run=client \
  -o yaml > deployment.yaml
```

---

## Nodes

```bash
# List nodes
kubectl get nodes

# Detailed node information
kubectl describe node <node>

# Show node IPs and additional information
kubectl get nodes -o wide

# Show resource usage
kubectl top nodes
```

### Cordon / Drain

```bash
# Prevent new pods from being scheduled
kubectl cordon <node>

# Evict pods and mark node unschedulable
kubectl drain <node>

# Make node schedulable again
kubectl uncordon <node>
```

> `kubectl drain` can evict workloads and should be used carefully, especially in production.

---

## Resource Usage

Requires the Kubernetes Metrics API / Metrics Server.

```bash
# Node CPU and memory usage
kubectl top nodes

# Pod CPU and memory usage
kubectl top pods

# Specific namespace
kubectl top pods -n <namespace>

# Sort by CPU
kubectl top pods --sort-by=cpu

# Sort by memory
kubectl top pods --sort-by=memory
```

---

## Rollouts

```bash
# Check rollout status
kubectl rollout status deployment/myapp

# View rollout history
kubectl rollout history deployment/myapp

# Restart deployment
kubectl rollout restart deployment/myapp

# Undo latest rollout
kubectl rollout undo deployment/myapp

# Undo to specific revision
kubectl rollout undo deployment/myapp --to-revision=2

# Pause rollout
kubectl rollout pause deployment/myapp

# Resume rollout
kubectl rollout resume deployment/myapp
```

---

## Scaling

```bash
# Scale deployment
kubectl scale deployment myapp --replicas=5

# Scale multiple deployments
kubectl scale deployment app1 app2 --replicas=3
```

### Horizontal Pod Autoscaler

```bash
# Create HPA
kubectl autoscale deployment myapp \
  --min=2 \
  --max=10 \
  --cpu-percent=70

# List HPAs
kubectl get hpa

# Describe HPA
kubectl describe hpa <hpa>
```

---

## Jobs and CronJobs

### Jobs

```bash
# Create a job
kubectl create job my-job --image=busybox -- echo "Hello"

# List jobs
kubectl get jobs

# Describe job
kubectl describe job my-job

# Delete job
kubectl delete job my-job
```

### CronJobs

```bash
# List CronJobs
kubectl get cronjobs

# Describe CronJob
kubectl describe cronjob <cronjob>

# Manually trigger a CronJob
kubectl create job --from=cronjob/<cronjob> manual-run

# Delete CronJob
kubectl delete cronjob <cronjob>
```

---

## Persistent Storage

### Persistent Volumes

```bash
kubectl get pv
kubectl describe pv <pv>
```

### Persistent Volume Claims

```bash
kubectl get pvc
kubectl describe pvc <pvc>
```

### Storage Classes

```bash
kubectl get storageclass
kubectl describe storageclass <storage-class>
```

---

## Labels and Annotations

```bash
# Add a label
kubectl label pod <pod> environment=production

# Remove a label
kubectl label pod <pod> environment-

# Add an annotation
kubectl annotate pod <pod> description="My application"

# Remove an annotation
kubectl annotate pod <pod> description-
```

---

## Output Formats

### Wide Output

```bash
kubectl get pods -o wide
```

### YAML

```bash
kubectl get pod <pod> -o yaml
```

### JSON

```bash
kubectl get pod <pod> -o json
```

### JSONPath

```bash
kubectl get pods \
  -o jsonpath='{.items[*].metadata.name}'
```

Get a pod's IP:

```bash
kubectl get pod <pod> \
  -o jsonpath='{.status.podIP}'
```

Get a node name:

```bash
kubectl get pod <pod> \
  -o jsonpath='{.spec.nodeName}'
```

### Custom Columns

```bash
kubectl get pods \
  -o custom-columns='NAME:.metadata.name,STATUS:.status.phase,IP:.status.podIP'
```

---

## Watching Resources

```bash
# Continuously watch pods
kubectl get pods --watch

# Watch deployments
kubectl get deployments --watch

# Watch services
kubectl get services --watch
```

---

## Events

Events are extremely useful when troubleshooting scheduling, image pulls, mounts, and container startup.

```bash
# List events
kubectl get events

# Events sorted by creation time
kubectl get events --sort-by='.lastTimestamp'

# Events in a namespace
kubectl get events -n <namespace>

# Events across all namespaces
kubectl get events -A
```

---

## Troubleshooting Pods

A useful troubleshooting sequence:

```bash
# 1. Check pod status
kubectl get pods

# 2. Get additional information
kubectl get pods -o wide

# 3. Describe the pod
kubectl describe pod <pod>

# 4. Check logs
kubectl logs <pod>

# 5. Check previous container logs
kubectl logs <pod> --previous

# 6. Open a shell
kubectl exec -it <pod> -- /bin/sh

# 7. Check recent events
kubectl get events --sort-by='.lastTimestamp'
```

### Common Pod States

| Status | Typical Meaning |
|---|---|
| `Pending` | Waiting for scheduling or resources |
| `ContainerCreating` | Container is being created |
| `Running` | Pod is running |
| `Completed` | Container finished successfully |
| `CrashLoopBackOff` | Container repeatedly crashes |
| `ImagePullBackOff` | Kubernetes cannot pull the image |
| `ErrImagePull` | Image pull failed |
| `Terminating` | Pod is being deleted |

---

## Troubleshooting Deployments

```bash
# Check deployment
kubectl get deployment <deployment>

# Describe deployment
kubectl describe deployment <deployment>

# Check ReplicaSets
kubectl get replicasets

# Check rollout status
kubectl rollout status deployment/<deployment>

# Check rollout history
kubectl rollout history deployment/<deployment>

# Check pods belonging to deployment
kubectl get pods -l app=<app-label>
```

---

## Debugging with Ephemeral Containers

On supported Kubernetes versions:

```bash
kubectl debug -it <pod> \
  --image=busybox \
  --target=<container>
```

Create a temporary debugging copy of a pod:

```bash
kubectl debug <pod> \
  -it \
  --image=busybox \
  --copy-to=debug-pod
```

---

## Resource Definitions

```bash
# List resource types
kubectl api-resources

# Explain a resource
kubectl explain pod

# Explain a specific field
kubectl explain pod.spec.containers

# Recursive explanation
kubectl explain pod.spec --recursive
```

---

## Useful Aliases

Add commonly used aliases to your shell configuration:

```bash
alias k='kubectl'
alias kgp='kubectl get pods'
alias kgs='kubectl get services'
alias kgd='kubectl get deployments'
alias kgn='kubectl get nodes'
alias kga='kubectl get all'
alias kd='kubectl describe'
alias kl='kubectl logs'
alias ke='kubectl exec -it'
```

Examples:

```bash
k get pods
kgp -A
kd pod my-pod
kl my-pod
ke my-pod -- /bin/sh
```

---

## Common Workflows

### Deploy an Application

```bash
kubectl apply -f deployment.yaml

kubectl get deployments

kubectl get pods

kubectl rollout status deployment/myapp

kubectl get services
```

### Update an Application

```bash
kubectl set image deployment/myapp \
  mycontainer=myimage:v2

kubectl rollout status deployment/myapp

kubectl rollout history deployment/myapp
```

### Roll Back an Application

```bash
kubectl rollout history deployment/myapp

kubectl rollout undo deployment/myapp

kubectl rollout status deployment/myapp
```

### Access an Application Locally

```bash
kubectl port-forward service/myapp 8080:80
```

Then:

```text
http://localhost:8080
```

### Restart an Application

```bash
kubectl rollout restart deployment/myapp

kubectl rollout status deployment/myapp
```

---

## Quick Troubleshooting Checklist

```bash
# What context am I using?
kubectl config current-context

# What namespace am I using?
kubectl config view --minify --output 'jsonpath={..namespace}'

# What is running?
kubectl get pods

# Where is it running?
kubectl get pods -o wide

# Why isn't it running?
kubectl describe pod <pod>

# What are the logs?
kubectl logs <pod>

# Did the previous container crash?
kubectl logs <pod> --previous

# What happened recently?
kubectl get events --sort-by='.lastTimestamp'

# What resources are available?
kubectl get nodes

# How much CPU/memory is being used?
kubectl top pods
kubectl top nodes
```

---

## Command Cheat Sheet

| Task | Command |
|---|---|
| Current context | `kubectl config current-context` |
| Switch context | `kubectl config use-context <context>` |
| List namespaces | `kubectl get ns` |
| List pods | `kubectl get pods` |
| List all pods | `kubectl get pods -A` |
| List deployments | `kubectl get deploy` |
| List services | `kubectl get svc` |
| List nodes | `kubectl get nodes` |
| Describe resource | `kubectl describe <type> <name>` |
| Get YAML | `kubectl get <type> <name> -o yaml` |
| View logs | `kubectl logs <pod>` |
| Follow logs | `kubectl logs -f <pod>` |
| Shell into pod | `kubectl exec -it <pod> -- /bin/sh` |
| Copy files | `kubectl cp <pod>:/path ./path` |
| Port forward | `kubectl port-forward <pod> 8080:80` |
| Apply manifest | `kubectl apply -f <file>` |
| Delete manifest | `kubectl delete -f <file>` |
| Scale deployment | `kubectl scale deploy <name> --replicas=3` |
| Restart deployment | `kubectl rollout restart deploy/<name>` |
| Roll back deployment | `kubectl rollout undo deploy/<name>` |
| Watch resources | `kubectl get pods -w` |
| View events | `kubectl get events` |
| Node usage | `kubectl top nodes` |
| Pod usage | `kubectl top pods` |
| Explain resource | `kubectl explain <resource>` |
