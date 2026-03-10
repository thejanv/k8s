# CKA Memory Note - Core Concept

In production Kubernetes environments, engineers typically create Deployments instead of Pods directly, allowing Kubernetes to automatically manage scaling, updates, and availability.

- Pods → smallest deployable unit
- RC → maintains pods manually (old style)
- RS → ensures N pods, works with Deployment
- Deployment → manages RS → Pods, supports rolling updates & rollbacks, preferred in production
- NodePort range: 30000–32767

Pods → RC → RS → Deployment
(Pods = smallest unit, RC = old style, RS = ensures N pods, Deployment = manages RS + rolling updates)

## Workloads

### Pods

Usually one container, but can run multiple containers.

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: app
  labels:
    tier: front-end
    app: myapp
spec:
  containers:
    - name: nginx-container
      image: nginx
```

```bash
kubectl create -f pod.yaml
kubectl get pods
kubectl describe pod app
kubectl delete pod app
```

### Replication Controller (RC)

Maintains N pods, old style.

```yaml
apiVersion: v1
kind: ReplicationController

metadata:
  name: app-rc
  labels:
    app: myapp
    tier: front
spec:
  template:
    metadata:
      labels:
        tier: front
        app: myapp
    spec:
      containers:
        - name: nginx-container
          image: nginx
  replicas: 3
```

```bash
kubectl create -f rc.yaml
kubectl get rc
kubectl describe rc app-rc
kubectl scale rc app-rc --replicas=5
kubectl delete rc app-rc
```

### Replica Set (RS)

Newer RC, usually managed by Deployment

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: app-rs
  labels:
    app: myapp
    tier: front
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      tier: front
  template:
    metadata:
      labels:
        app: myapp
        tier: front
    spec:
      containers:
        - name: nginx-container
          image: nginx
```

```bash
kubectl create -f rs.yaml
kubectl get rs
kubectl describe rs app-rs
kubectl scale rs app-rs --replicas=6
kubectl replace -f rs.yaml
kubectl apply -f rs.yaml
kubectl delete rs app-rs
```

### Deployment

Manages ReplicaSets automatically; supports rolling updates and rollbacks.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-deploy
  labels:
    app: myapp
    tier: front
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      tier: front
  template:
    metadata:
      labels:
        app: myapp
        tier: front
    spec:
      containers:
        - name: nginx-container
          image: nginx
```

```bash
kubectl get deployments
kubectl get all
kubectl describe deployment app-deploy
kubectl scale deployment app-deploy --replicas=5
kubectl rollout status deployment app-deploy
kubectl rollout undo deployment app-deploy
kubectl delete deployment app-deploy
```

## Dry-run examples for exam

```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-pod.yaml
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > nginx-deployment.yaml
kubectl create deployment --image=nginx nginx --replicas=4 --dry-run=client -o yaml > nginx-deployment.yaml
```

## Services

Services provide stable networking for Pods.

### NodePort

Exposes application outside the cluster using Node IP + Port.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  selector:
    app: myapp
    tier: front-end
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
```

```bash
kubectl create -f nodeport.yaml
kubectl get services
```

### ClusterIP

Used for internal pod communication.

```yaml
apiVersion: v1
kind: Service

metadata:
  name: back-end

spec:
  type: ClusterIP

  selector:
    app: my-app

  ports:
    - port: 80
      targetPort: 80
```

### Load Balancer

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-lb
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
    - targetPort: 80
      port: 80
```
