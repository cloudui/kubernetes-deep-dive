# Scaling

Scale up more pods on more demand (CPU, requests, connections). Scale down on less demand. Simple.

You need more nodes for more pods. 

- More computers to actually run the pods if demand overwhelms your nodes.

Pods are sent to pending if nodes are full. The pending checker will check now and then and add nodes as necessary.

# Horizontal Pod Autoscaler

All about Pods. 

Horizontal = more of the same pod

![Untitled](Scaling%206bdf8e9e55a745bdbe5d67fa84a3faeb/Untitled.png)

1 HPA per deployment

![Screen Shot 2022-08-06 at 11.21.28 AM.png](Scaling%206bdf8e9e55a745bdbe5d67fa84a3faeb/Screen_Shot_2022-08-06_at_11.21.28_AM.png)

- Create pods with resource requests
- Asking for 0.2 CPUs (”200m” 200 millicores)
- If pods uses more than 10% (50% of 200 millicores), it will scale up
- The HPA doesn’t know how to scale, only controls it

## Demo

```yaml
# Name space: not security boundaries
apiVersion: v1
kind: Namespace
metadata:
  name: acg-ns
---
# Service: Load Balancer
apiVersion: v1
kind: Service
metadata:
  namespace: acg-ns
  name: acg-lb
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: acg-stress
---
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: acg-ns
  labels:
    app: acg-stress
  name: acg-web
spec:
  selector:
    matchLabels:
      app: acg-stress
  replicas: 1
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: acg-stress
    spec:
      containers:
      - image: k8s.gcr.io/hpa-example
        name: stresser
        ports:
          - containerPort: 80
        resources:
          requests:
            cpu: 0.2
---
# HPA
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: acg-hpa
  namespace: acg-ns
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: acg-web
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 50
```

### Commands in Order

```bash
kubectl apply -f hpademo.yml

kubectl get deploy --namespace acg-ns

kubectl get hpa --namespace acg-ns

kubectl get nodes 

# Watch nodes
kubectl get hpa --namespace acg-ns --watch

# Check deployment made by HPA updater
# Will have updated replicas based on load
# Config all handled by control plane
kubectl get deploy --namespace acg-ns -o yaml

# -----------------------

# Stressing the above Nodes via busybox
kubectl run - i --ty loader --image=busybox /bin/sh

# Sample script
while true; do wget -q -O- http://acg-lb.acg-ns.svc.cluster.local; done
```

# Cluster Autoscaler

Very dependent on cloud provider. Tied to its K8s service

Always use Resource Requests

- Will not scale without it

Scaling is only based on requests (0.5 CPU), not how much it’s actually using (if it’s only using 0.01 CPU)

Resource **request** is how much it is guaranteed to get

- Will only schedule it on a node that can give it the requirements

Resource **limit** is the max it can get. It will be restricted once it reaches the limit. 

[https://cloud.google.com/blog/products/containers-kubernetes/kubernetes-best-practices-resource-requests-and-limits](https://cloud.google.com/blog/products/containers-kubernetes/kubernetes-best-practices-resource-requests-and-limits)

Pods will be put into pending if resources are not available (nodes are full)

```bash
kubectl get pods
```

Will spin up nodes necessary to give more resources to pods. 

# Summary

New API (probably out now) can support more than CPU load

- Memory and **custom metrics**