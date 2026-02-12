# DevOps & Infrastructure

Comprehensive DevOps tools, container orchestration, CI/CD pipelines, and infrastructure automation guides.

---

## 🚀 Core Platforms

### Docker - Container Technology

- [Basics](docker/basics/index.md) - Docker concepts, architecture, installation
- [Images](docker/images/index.md) - Building, managing, and optimizing Docker images
- [Containers](docker/containers/index.md) - Container lifecycle and management
- [Networking](docker/networking/index.md) - Container communication and networks
- [Storage & Volumes](docker/storage/index.md) - Data persistence and storage
- [Docker Compose](docker/compose/index.md) - Multi-container orchestration

### Kubernetes - Container Orchestration

- [Concepts](kubernetes/index.md) - Pods, Services, Deployments
- [Cluster Management](kubernetes/index.md) - Node management and RBAC
- [Networking](kubernetes/index.md) - Service discovery and networking
- [Storage](kubernetes/index.md) - Persistent volumes and storage classes
- [Deployment Strategies](kubernetes/index.md) - Rolling updates and blue-green deployments

### CI/CD - Continuous Integration & Deployment

- [Pipeline Design](cicd/index.md) - GitLab CI, GitHub Actions, Jenkins
- [Build Automation](cicd/index.md) - Build pipelines and artifact management
- [Testing & Quality](cicd/index.md) - Automated testing and code quality
- [Deployment Automation](cicd/index.md) - Automated deployments and rollbacks
- [Monitoring & Alerts](cicd/index.md) - Pipeline monitoring and notifications

---

## 🔧 Quick Command Reference

### Docker Commands
```bash
# Image Management
docker images                           # List images
docker build -t app:1.0 .              # Build image from Dockerfile
docker tag app:1.0 registry/app:1.0    # Tag image for registry
docker push registry/app:1.0           # Push to registry
docker pull registry/app:1.0           # Pull from registry

# Container Operations
docker run -d -p 8080:80 app:1.0       # Run container in background
docker ps                              # List running containers
docker ps -a                           # List all containers
docker logs -f container_id            # View logs in real-time
docker exec -it container_id bash      # Execute command in container
docker stop container_id               # Stop container gracefully
docker kill container_id               # Force stop container
docker rm container_id                 # Remove container

# Networking
docker network create mynet            # Create custom network
docker network ls                      # List networks
docker network inspect mynet           # Inspect network details
docker run -d --network mynet app:1.0  # Run container on network

# Volumes
docker volume create mydata            # Create volume
docker volume ls                       # List volumes
docker volume inspect mydata           # Inspect volume
docker run -v mydata:/data app:1.0    # Mount volume to container

# Container Information
docker inspect container_id            # Get container details
docker stats                           # Real-time resource usage
docker top container_id                # View processes in container
```

### Docker Compose Commands
```bash
# Start/Stop Services
docker-compose up -d                   # Start services in background
docker-compose down                    # Stop and remove containers
docker-compose restart                 # Restart services
docker-compose pause                   # Pause services
docker-compose unpause                 # Resume services

# Service Management
docker-compose ps                      # List services
docker-compose logs -f                 # View logs
docker-compose logs -f service_name    # Service-specific logs
docker-compose exec service bash       # Execute command in service
docker-compose build                   # Build/rebuild images
docker-compose pull                    # Pull images

# Configuration
docker-compose up -d --scale app=3     # Scale service replicas
docker-compose config                  # Validate compose file
```

### Kubernetes Commands
```bash
# Cluster Information
kubectl cluster-info                   # Cluster details
kubectl version                        # Client and server versions
kubectl get nodes                      # List cluster nodes
kubectl describe node node_name        # Node details

# Pod Management
kubectl get pods                       # List pods
kubectl get pods -A                    # List all namespaces
kubectl describe pod pod_name          # Pod details
kubectl logs pod_name                  # View pod logs
kubectl logs -f pod_name               # Follow pod logs
kubectl exec -it pod_name -- bash      # Shell into pod
kubectl delete pod pod_name            # Delete pod
kubectl port-forward pod_name 8080:80  # Port forward

# Deployment Management
kubectl create deployment app --image=app:1.0  # Create deployment
kubectl get deployments                # List deployments
kubectl scale deployment app --replicas=3      # Scale deployment
kubectl rollout status deployment/app  # Check rollout status
kubectl rollout history deployment/app # Deployment history
kubectl rollout undo deployment/app    # Rollback deployment
kubectl set image deployment/app app=app:2.0  # Update image

# Service Management
kubectl expose deployment app --type=LoadBalancer --port=80 --target-port=8080
kubectl get services                   # List services
kubectl describe service app           # Service details

# ConfigMaps & Secrets
kubectl create configmap config --from-literal=key=value
kubectl create secret generic secret --from-literal=password=pass
kubectl get configmaps                 # List configmaps
kubectl get secrets                    # List secrets

# Namespace Management
kubectl get namespaces                 # List namespaces
kubectl create namespace custom        # Create namespace
kubectl get pods -n custom             # Pods in namespace
kubectl delete namespace custom        # Delete namespace

# Resource Management
kubectl apply -f deployment.yaml       # Apply manifests
kubectl delete -f deployment.yaml      # Delete manifests
kubectl get all                        # All resources
```

### Common CI/CD Operations
```bash
# GitLab CI
git push origin main                   # Trigger CI pipeline
gitlab-runner install                  # Install GitLab Runner
gitlab-runner start                    # Start runner
gitlab-runner verify                   # Verify runner

# GitHub Actions
git push origin main                   # Trigger workflow
gh run list                            # List workflow runs
gh run view run_id                     # View run details

# Jenkins
jenkins-cli -s http://jenkins build job_name
jenkins-cli -s http://jenkins queue
```

---

## 📚 Common Scenarios

### Deploy Docker Container with Docker Compose
```yaml
version: '3.8'
services:
  app:
    image: myapp:1.0
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgres://db:5432/mydb
    depends_on:
      - db
  db:
    image: postgres:13
    environment:
      - POSTGRES_PASSWORD=secret
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

### Deploy to Kubernetes
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:1.0
        ports:
        - containerPort: 8080
        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"
---
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8080
```

### Basic GitLab CI Pipeline
```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - docker build -t myapp:$CI_COMMIT_SHA .
    - docker push myapp:$CI_COMMIT_SHA

test:
  stage: test
  script:
    - docker run myapp:$CI_COMMIT_SHA npm test

deploy:
  stage: deploy
  script:
    - kubectl set image deployment/myapp myapp=myapp:$CI_COMMIT_SHA
  environment:
    name: production
```

---

## 🎯 DevOps Workflow

1. **Plan** - Define infrastructure and deployment strategy
2. **Code** - Write application code and infrastructure-as-code
3. **Build** - Containerize application with Docker
4. **Test** - Run automated tests in CI pipeline
5. **Deploy** - Deploy to Kubernetes cluster
6. **Monitor** - Monitor application and infrastructure
7. **Feedback** - Analyze metrics and iterate

---

## 🔗 Integration Points

### Local Development
- Docker for local containerization
- Docker Compose for multi-container setup
- Docker images pushed to registry

### CI/CD Pipeline
- Git push triggers CI pipeline
- Build Docker images from Dockerfile
- Run tests in containers
- Push images to registry
- Deploy to Kubernetes

### Production
- Kubernetes orchestrates containers
- LoadBalancer services expose applications
- Persistent volumes for data
- ConfigMaps and Secrets for configuration
- Monitoring and logging integration

---

## 📖 Learning Path

1. **Docker Basics** - Container concepts and commands
2. **Docker Images** - Build and manage images
3. **Docker Compose** - Multi-container applications
4. **Kubernetes Basics** - Cluster concepts and API objects
5. **Kubernetes Deployments** - Deploy and manage applications
6. **CI/CD Pipelines** - Automate build and deployment
7. **Advanced** - Helm, networking, storage, security

---

## 🔗 Additional Resources

- [Linux](../linux/index.md) - Base OS knowledge
- [Networking](../networking/index.md) - Network concepts
- [Databases](../databases/index.md) - Database deployment
- [QA & Best Practices](../qa/index.md) - DevOps best practices

---

**Tags:** `#devops` `#docker` `#kubernetes` `#cicd` `#containerization` `#orchestration`
