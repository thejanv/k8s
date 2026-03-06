## Pods in Kubernetes (K8s)

### Example 1: Creating a Simple Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

**Explanation:**

- `apiVersion` and `kind` are string values.
- `metadata` is a dictionary (object) that contains details such as `name` and `labels`.
- `spec` defines the desired state of the Pod.
- `containers` lists the containers running inside the Pod.

**Commands:**

```bash
kubectl create -f pod.yaml
kubectl get pods
kubectl describe pod myapp-pod
```

---

### Editing the Pod Configuration

```bash
vim pod.yaml
```

### Example 2: Another Pod Definition

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
    tier: frontend
spec:
  containers:
    - name: nginx
      image: nginx
```

**Apply the configuration:**

```bash
kubectl apply -f pod.yaml
kubectl get pods
```

This will create or update the Pod based on the configuration defined in `pod.yaml`.
