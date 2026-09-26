# kubernetes-nginx
Nginx Kubernetes manifests and GitHub Actions workflow
# Kubernetes Nginx Pod with ClusterIP Service

This project demonstrates how to deploy an Nginx Pod and expose it internally within a Kubernetes cluster using a ClusterIP Service.

## Architecture

```text
Namespace (dev)
│
├── Nginx Pod
│     └── Label: app=nginx
│
└── ClusterIP Service
      └── Selector: app=nginx
```

Traffic Flow:

```text
Client Pod
    |
    v
nginx-clusterip Service
    |
    v
nginx-pod
    |
    v
Nginx Container (Port 80)
```

---

## Manifest Components

### 1. Namespace

Creates a dedicated namespace called `dev`.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

### 2. Pod

Deploys a single Nginx Pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: dev
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

### 3. ClusterIP Service

Exposes the Pod internally within the cluster.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-clusterip
  namespace: dev
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
```

---

## Understanding the Selector

The Service uses the following selector:

```yaml
selector:
  app: nginx
```

The selector tells Kubernetes which Pods should receive traffic from the Service.

The Pod contains the matching label:

```yaml
labels:
  app: nginx
```

Because the label and selector match, Kubernetes automatically routes traffic from the Service to the Nginx Pod.

---

## Prerequisites

- Kubernetes Cluster
- kubectl installed and configured
- Access to create resources in the cluster

---

## Deployment

Apply the manifest:

```bash
kubectl apply -f nginx-clusterip.yaml
```

Verify the resources:

```bash
kubectl get namespaces
kubectl get pods -n dev
kubectl get svc -n dev
```

Expected output:

```text
NAME         READY   STATUS
nginx-pod    1/1     Running

NAME              TYPE        CLUSTER-IP
nginx-clusterip   ClusterIP   10.x.x.x
```

---

## Testing the Service

Launch a temporary test Pod:

```bash
kubectl run test-pod \
  --image=busybox \
  -it \
  --rm \
  --restart=Never \
  -n dev -- sh
```

Inside the Pod:

```sh
wget -qO- http://nginx-clusterip
```

You should see the default Nginx welcome page HTML.

---

## Useful Commands

View Pod details:

```bash
kubectl describe pod nginx-pod -n dev
```

View Service details:

```bash
kubectl describe svc nginx-clusterip -n dev
```

View logs:

```bash
kubectl logs nginx-pod -n dev
```

Delete resources:

```bash
kubectl delete -f nginx-clusterip.yaml
```

---

## Key Kubernetes Concepts

| Component | Purpose |
|------------|----------|
| Namespace | Logical isolation of resources |
| Pod | Smallest deployable Kubernetes unit |
| Label | Key-value pair attached to resources |
| Selector | Matches labels to identify Pods |
| Service | Provides stable network access |
| ClusterIP | Internal-only service exposure |

---

## Repository Structure

```text
kubernetes-nginx/
│
├── README.md
└── nginx-clusterip.yaml
```

## Author
**Kadiri Sandeepkumar**  
