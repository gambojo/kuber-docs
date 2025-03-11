# kuber-docs

## Pod
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: pod
  labels:
    app: pod
    type: front
spec:
  containers:
  - name: pod
    image: nginx
```

## ReplicaSet
```yaml
---
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: ReplicaSet
  labels:
    app: ReplicaSet
    type: front
spec:
  template:
    metadata:
      name: pod
      labels:
        app: pod
        type: front
    spec:
      containers:
        - name: pod
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: front
```

## Deployment
```yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: Deployment
  labels:
    app: Deployment
    type: front
spec:
  template:
    metadata:
      name: pod
      labels:
        app: pod
        type: front
    spec:
      containers:
        - name: pod
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: front
```

## Namespace
```yaml
---
apiVersion: v1
kind: Namespace
metadata:
  name: Namespace
```

## ResourceQuota
```yaml
---
apiVersion: v1
kind: ResourceQuota
metadata:
  name: ResourceQuota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```

## Service
### NodePort
```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: Service
spec:
  type: NodePort
  ports:
  - targetPort: 80
    port: 80
    nodePort: 30008
  selector:
    app: pod
    type: front
```
### ClusterIP
```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: Service
spec:
  type: ClusterIP
  ports:
  - targetPort: 80
    port: 80
  selector:
    app: pod
    type: front
```

### LoadBalancer
```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: Service
spec:
  type: LoadBalancer
  ports:
  - targetPort: 80
    port: 80
  selector:
    app: pod
    type: front
```

## Manual Scheduling
### Binding
```yaml
---
apiVersion: v1
kind: Binding
metadata:
  name: pod-name
target:
  apiVersion: v1
  kind: Node
  name: worker-node-1
```
### In pod
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: pod
  labels:
    app: pod
    type: front
spec:
  containers:
  - name: pod
    image: nginx
  nodeName: worker-node-1
```

## Taints and Tolerations
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: pod
  labels:
    app: pod
    type: front
spec:
  containers:
  - name: pod
    image: nginx
  tolerations:
  - key: "app"
    operator: "Equal" # In; NotIn
    value: "green"
    effect: "NoSchedule" # NoExecute; PreferNoSchedule
```

## NodeSelector
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: pod
  labels:
    app: pod
    type: front
spec:
  containers:
  - name: pod
    image: nginx
  nodeSelector:
    size: large # <- Its node label
```

## NodeAffinity
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: pod
  labels:
    app: pod
    type: front
spec:
  containers:
  - name: pod
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution: # preferredDuringSchedulingIgnoredDuringExecution; requiredDuringSchedulingRequiredDuringExecution
        nodeSelectorTerms:
        - matchExpressions:
          - key:
            operator: In # NotIn; Exists (If oper == Exists, then values not use)
            values:
            - large
```

## PodAffinity
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: pod
  labels:
    app: pod
    type: front
spec:
  containers:
  - name: pod
    image: nginx
  affinity:
    podAntiAffinity: # podAffinity
      requiredDuringSchedulingIgnoredDuringExecution: # preferredDuringSchedulingIgnoredDuringExecution; requiredDuringSchedulingRequiredDuringExecution
      - labelSelector:
          matchExpressions:
          - key:
            operator: In # NotIn; Exists (If oper == Exists, then values not use)
            values:
            - large
          topologyKey: kubernetes.io/hostname
```

## Resource Requirements and Limits
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: pod
spec:
  containers:
  - name: pod
    image: nginx
    ports:
    - containerPort: 8080
    resources:
      requests:
        memory: "1Gi"
        cpu: 1
      limits:
        memory: "2Gi"
        cpu: 2
---
apiVersion: v1
kind: LimitRange
metadata:
  name: limit
spec:
  limits:
  - default:
      memory: 512Mi
    defautRequest:
      memory: 256Mi
    type: Container
```
