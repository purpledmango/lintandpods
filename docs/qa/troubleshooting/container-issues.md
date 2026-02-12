# Container & DevOps Issues

Docker, Kubernetes, and container-related troubleshooting.

---

## Docker Build Fails

### Question: Why does my Docker build fail?

**Common Errors:**

```bash
# Error 1: Base image not found
FAILED: "FROM nonexistent/image:latest"
Solution: docker pull nonexistent/image:latest  # or use correct image

# Error 2: Command not found in layer
RUN apt-get install package
Solution: RUN apt-get update && apt-get install -y package

# Error 3: Layer caching issues
Solution: docker build --no-cache -t image:tag .

# Error 4: Docker daemon connection
Solution: Check if Docker is running: sudo systemctl status docker
```

**Diagnostic Steps:**

```bash
# Check Docker daemon
systemctl status docker
docker ps

# Check images
docker images

# Check build logs
docker build -t image:tag .  # See full output

# Check disk space
docker system df

# Check layer size
docker image inspect image:tag
```

**Common Solutions:**

```bash
# Update base image
docker pull ubuntu:20.04

# Clean up unused images/layers
docker system prune -a

# Build with verbose output
docker build --progress=plain -t image:tag .

# Check Dockerfile syntax
docker run --rm -i hadolint/hadolint < Dockerfile
```

**Tags:** `#docker` `#build` `#troubleshooting`

---

## Pod Keeps Crashing / CrashLoopBackOff

### Question: Why is my Kubernetes pod in CrashLoopBackOff?

**Symptoms:**
- Pod status shows CrashLoopBackOff
- Container restarts every few seconds

**Diagnostic Steps:**

```bash
# Check pod status
kubectl get pods
kubectl describe pod pod_name

# View pod logs
kubectl logs pod_name
kubectl logs pod_name --previous  # previous attempt

# Follow logs
kubectl logs -f pod_name

# Get more details
kubectl get pods -o wide
kubectl get events -n default
```

**Common Causes & Solutions:**

| Cause | Fix |
|-------|-----|
| Missing image | `kubectl set image pod/name container=image:tag` |
| Port already in use | Check service port config |
| Insufficient resources | `kubectl describe pod` check limits |
| Config/Secrets missing | Check if ConfigMap/Secret exists |
| Application error | Review logs: `kubectl logs pod_name` |
| Readiness probe failing | Check probe configuration |

**Resolution:**
```bash
# Check why pod crashed
kubectl logs pod_name --tail=50

# Deploy corrected version
kubectl set image deployment/app app=myapp:v2

# Or scale down and check
kubectl scale deployment/app --replicas=0
kubectl scale deployment/app --replicas=1
```

**Tags:** `#kubernetes` `#troubleshooting` `#pods`

---

## Image Won't Push to Registry

### Question: Why can't I push image to Docker registry?

**Common Errors:**

```bash
# Error 1: Not logged in
denied: requested access to the resource is denied
Solution: docker login registry.example.com

# Error 2: Wrong image name
Failed to resolve ref
Solution: docker tag local_image:tag registry/image:tag

# Error 3: Registry unreachable
Connection refused or timeout
Solution: Check registry host, DNS, firewall

# Error 4: No write permission
access denied
Solution: Check credentials and permissions
```

**Diagnostic Steps:**

```bash
# Check Docker login
cat ~/.docker/config.json

# Test registry connectivity
curl -k https://registry.example.com/v2/

# Check image name
docker images | grep image_name

# Verify credentials
docker login registry.example.com
```

**Resolution:**

```bash
# Login to registry
docker login registry.example.com

# Tag image correctly
docker tag myapp:1.0 registry.example.com/myapp:1.0

# Push to registry
docker push registry.example.com/myapp:1.0

# For private registries
docker login --username user --password pass registry.example.com
```

**Tags:** `#docker` `#registry` `#troubleshooting`

---

## Container Can't Connect to Service

### Question: Why can't my container reach another service?

**Diagnostic:**

```bash
# Inside container
kubectl exec -it pod_name -- bash

# Test connectivity
ping other-pod
curl other-service:port
nslookup other-service

# Check service
kubectl get svc
kubectl describe svc service_name

# Check endpoints
kubectl get endpoints service_name
```

**Common Causes:**

| Issue | Solution |
|-------|----------|
| Service doesn't exist | Create service or check name |
| Pod not in service | Check selector labels |
| Port mismatch | Verify targetPort and port |
| Network policy blocking | Review network policies |
| Namespace issue | Services in different namespaces need FQDN |

**Fix:**
```bash
# Use correct DNS name
# Same namespace: service_name
# Different namespace: service_name.namespace.svc.cluster.local

# Check labels match selector
kubectl get pods --show-labels
kubectl get svc service_name -o yaml | grep selector
```

**Tags:** `#kubernetes` `#networking` `#services`

---

## Persistent Volume Not Mounting

### Question: Why isn't my PV mounting in pod?

**Diagnostic:**

```bash
# Check PV and PVC
kubectl get pv
kubectl get pvc
kubectl describe pv pv_name
kubectl describe pvc pvc_name

# Check pod events
kubectl describe pod pod_name

# Check logs
kubectl logs pod_name
```

**Common Causes:**

| Cause | Solution |
|-------|----------|
| PVC not bound | PV and PVC selectors don't match |
| Storage class unavailable | Create storage class or use existing |
| Permission denied | Check volume permissions and owner |
| Node not ready | Check node status with `kubectl get nodes` |

**Tags:** `#kubernetes` `#storage` `#troubleshooting`

---

**Tags:** `#troubleshooting` `#docker` `#kubernetes` `#devops`
