# Kubernetes

Kubernetes orchestration, deployment, and cluster management guides.

## Quick Links

### Core Concepts
- Pods
- Services
- Deployments
- ConfigMaps & Secrets
- Namespaces

### Cluster Management
- Node management
- Networking
- Storage
- RBAC
- Monitoring

---

## Common Tasks

### Deploy an Application
```bash
kubectl create deployment myapp --image=myimage:tag
```

### Expose a Service
```bash
kubectl expose deployment myapp --type=LoadBalancer --port=80 --target-port=8080
```

### View Pods
```bash
kubectl get pods -A
```

### View Logs
```bash
kubectl logs -f pod/mypod
```

---

## Related Resources
- [Docker](../docker/index.md) - Container basics
- [Linux](../../linux/index.md) - Base OS knowledge

---

**Tags:** `#kubernetes` `#k8s` `#orchestration` `#deployment`
