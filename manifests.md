# kuber manifests

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

## DaemonSet
```yaml
---
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: DaemonSet
  labels:
    app: DaemonSet
    type: front
spec:
  selector:
    matchLabels:
      app: pod
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
```

## Static Pods
### Scheduler
#### Kubeconfig custom-scheduler
```yaml
---
apiVersion: v1
kind: Pod
metadata:
  name: custom-scheduler
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-scheduler
    - --bind-address=127.0.0.1
    - kubeconfig=/etc/kubernetes/scheduler.conf
    - config=/etc/kubernetes/custom-scheduler.yml
    name: kube-scheduler
    image: kube-scheduler:v1.30.3
```
#### Config custom-scheduler
```yaml
---
apiVersion: kubescheduler.config.k8s.io/v1
kind: CubeSchedulerConfiguration
profiles:
- schedulerName: custom-scheduler
leaderElection:
  leaderElect: true # If replics > 1
  resourceNamespace: kube-system
  resourceName: custom-scheduler
```
#### Pod with custom scheduler
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
  schedulerName: custom-scheduler
```

#### PriorityClass
```yaml
---
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
  name: high-priority
value: 1000000
preemptionPolicy: Never
globalDefault: false
description: "Our cristal app"

---
apiVersion: v1
kind: Pod
metadata:
  name: pod
spec:
  priorityClassName: high-priority
  containers:
  - name: pod
    image: nginx
```

#### Custom-scheduler with profiles
```yaml
---
apiVersion: kubescheduler.config.k8s.io/v1
kind: KubeSchedulerConfiguration
profiles:
- schedulerName: custom-scheduler
  plugins:
    preScore:
      enabled:
      - name: InterPodAffinity
      disabled:
      - name: CustomPlugin1
      - name: CustomPlugin2
    Score:
      disabled:
      - name: "*"
...
```
#### Custom-scheduler
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: my-scheduler
  name: my-scheduler
  namespace: kube-system
spec:
  serviceAccountName: my-scheduler
  containers:
  - command:
    - /usr/local/bin/kube-scheduler
    - --config=/etc/kubernetes/my-scheduler/my-scheduler-config.yaml
    image: registry.k8s.io/kube-scheduler:v1.30.1
    livenessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
      initialDelaySeconds: 15
    name: kube-second-scheduler
    readinessProbe:
      httpGet:
        path: /healthz
        port: 10259
        scheme: HTTPS
    resources:
      requests:
        cpu: '0.1'
    securityContext:
      privileged: false
    volumeMounts:
      - name: config-volume
        mountPath: /etc/kubernetes/my-scheduler
  hostNetwork: false
  hostPID: false
  volumes:
    - name: config-volume
      configMap:
        name: my-scheduler-config
```
