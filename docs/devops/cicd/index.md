# CI/CD - Continuous Integration & Deployment

Continuous Integration and Continuous Deployment pipeline tools, strategies, and best practices.

---

## Overview

CI/CD automates the process of building, testing, and deploying applications, enabling faster and more reliable software delivery.

### Benefits of CI/CD
- **Faster Releases** - Automate manual processes
- **Earlier Detection** - Catch bugs in early stages
- **Reduced Risk** - Smaller changes are easier to troubleshoot
- **Better Quality** - Automated testing at every stage
- **Collaboration** - Better team communication and workflow

---

## Popular CI/CD Platforms

### GitLab CI/CD
- Native to GitLab (cloud and self-hosted)
- GitLab Runners for distributed execution
- Built-in container registry
- Powerful YAML-based pipeline configuration

### GitHub Actions
- Native to GitHub
- GitHub-hosted runners (free tier)
- Marketplace with pre-built actions
- Workflow files in .github/workflows/

### Jenkins
- Open-source, self-hosted
- Extensive plugin ecosystem
- Declarative and scripted pipelines
- Distributed agent architecture

### Other Platforms
- CircleCI
- Travis CI
- Azure Pipelines
- AWS CodePipeline
- Atlassian Bamboo

---

## Basic Pipeline Stages

### 1. Build Stage
```bash
# Compile code, resolve dependencies
npm install
npm run build
# or
mvn clean compile
```

### 2. Test Stage
```bash
# Run unit and integration tests
npm test
pytest tests/
# Generate code coverage
```

### 3. Quality Stage
```bash
# Code quality analysis
sonar-scanner
eslint src/
pylint *.py
```

### 4. Deploy Stage
```bash
# Deploy to staging/production
kubectl apply -f k8s/
docker push registry/app:tag
```

### 5. Verify Stage
```bash
# Smoke tests, integration tests
curl http://app/health
selenium tests/
```

---

## GitLab CI Example

**.gitlab-ci.yml**
```yaml
stages:
  - build
  - test
  - quality
  - deploy

variables:
  IMAGE_NAME: $CI_REGISTRY_IMAGE
  IMAGE_TAG: $CI_COMMIT_SHA

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $IMAGE_NAME:$IMAGE_TAG .
    - docker push $IMAGE_NAME:$IMAGE_TAG

test:
  stage: test
  image: $IMAGE_NAME:$IMAGE_TAG
  script:
    - npm test
    - npm run coverage
  coverage: '/Coverage: \d+\.\d+%/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

quality:
  stage: quality
  image: node:16
  script:
    - npm install -g sonar-scanner
    - sonar-scanner -Dsonar.projectKey=myapp

deploy_staging:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl set image deployment/app-staging app=$IMAGE_NAME:$IMAGE_TAG
  environment:
    name: staging
  only:
    - develop

deploy_production:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl set image deployment/app-prod app=$IMAGE_NAME:$IMAGE_TAG
  environment:
    name: production
  only:
    - main
  when: manual
```

---

## GitHub Actions Example

**.github/workflows/deploy.yml**
```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '16'
      - run: npm ci
      - run: npm run build
      - run: npm test
      
  docker:
    needs: build
    runs-on: ubuntu-latest
    if: github.event_name == 'push'
    steps:
      - uses: actions/checkout@v2
      - uses: docker/setup-buildx-action@v1
      - uses: docker/login-action@v1
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/build-push-action@v2
        with:
          context: .
          push: true
          tags: ghcr.io/${{ github.repository }}:${{ github.sha }}

  deploy:
    needs: docker
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v2
      - uses: azure/setup-kubectl@v1
        with:
          version: v1.21.3
      - run: |
          kubectl config use-context docker-desktop
          kubectl set image deployment/app app=ghcr.io/${{ github.repository }}:${{ github.sha }}
```

---

## Best Practices

### Pipeline Design
- Keep pipelines fast (target: < 10 minutes)
- Fail fast - run quick checks first
- Parallelize jobs where possible
- Use caching to speed up builds
- Keep pipeline configuration in Git

### Testing Strategy
- Unit tests (fast, frequent)
- Integration tests (medium speed)
- End-to-end tests (slower, essential paths)
- Performance tests (before production)
- Security scanning

### Deployment Strategy
- Use environment gates (staging before production)
- Manual approval for production
- Blue-green or canary deployments
- Automated rollbacks
- Health checks and monitoring

### Security
- Scan dependencies for vulnerabilities
- SAST (Static Application Security Testing)
- DAST (Dynamic Application Security Testing)
- Secret management - never hardcode secrets
- Image scanning for vulnerabilities
- RBAC for pipeline access

---

## Common CI/CD Patterns

### Feature Branch Workflow
```
feature branch
    ↓
run CI tests
    ↓
create pull request
    ↓
review & approve
    ↓
merge to main
    ↓
deploy to production
```

### Trunk-Based Development
```
commit to main
    ↓
run CI tests
    ↓
feature flags control deployment
    ↓
gradual rollout
```

### GitOps Workflow
```
commit infrastructure/app code
    ↓
git push
    ↓
webhook triggers reconciliation
    ↓
desired state applied to cluster
```

---

## Integration with Kubernetes

### Deploy via kubectl
```bash
kubectl set image deployment/app container=image:tag
kubectl apply -f k8s/
kubectl rollout status deployment/app
```

### Using Helm in CI/CD
```bash
helm repo add myrepo https://charts.example.com
helm repo update
helm upgrade --install myapp myrepo/myapp --values values.yaml
```

### GitOps with ArgoCD
```bash
# ArgoCD watches Git repo for changes
# Automatically syncs cluster state to Git desired state
argocd app sync myapp
argocd app wait myapp
```

---

## Troubleshooting

### Pipeline Fails
- Check logs: `gitlab-runner logs` or GitHub Actions logs
- Run locally: `docker build .`, `npm test`
- Check environment variables
- Verify secrets are properly set
- Review recent commits

### Deployment Issues
- Verify image is in registry
- Check Kubernetes RBAC permissions
- Verify resource quotas
- Check logs in Kubernetes

### Performance Issues
- Enable caching: dependencies, Docker layers
- Use artifacts only when needed
- Parallelize jobs
- Use lightweight base images
- Consider self-hosted runners

---

**Tags:** `#cicd` `#ci-cd` `#devops` `#automation` `#gitops`
