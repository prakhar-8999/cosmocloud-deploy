# Kubernetes Microservices Deployment with Helm

This repository contains a Helm chart for deploying a microservices-based application stack consisting of a frontend, backend, and Redis database on Kubernetes.

## Project Structure

```
cosmocloud-deploy/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── frontend-deployment.yaml
    ├── frontend-service.yaml
    ├── backend-deployment.yaml
    ├── backend-service.yaml
    ├── redis-deployment.yaml
    └── redis-service.yaml
```

## Components

### 1. Frontend Service
- **Image**: `shreybatra/sample-frontend`
- **Type**: NodePort Service
- **External Port**: 31000
- **Internal Port**: 5175
- **Purpose**: Serves the web interface to end users
- **Environment Variables**:
  - `BACKEND_URL`: http://backend-svc:8000

### 2. Backend Service
- **Image**: `shreybatra/sample-backend`
- **Type**: ClusterIP Service
- **Port**: 8000
- **Purpose**: Handles business logic and communicates with Redis
- **Environment Variables**:
  - `REDIS_URI`: redis://redis-svc:6379

### 3. Redis Service
- **Image**: `redis:latest`
- **Type**: ClusterIP Service
- **Port**: 6379
- **Purpose**: Data storage layer

## Prerequisites

1. Install Kubernetes (Minikube)
```bash
# For Linux
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# For macOS
brew install minikube
```

2. Install Helm
```bash
# For Linux
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# For macOS
brew install helm
```

## Deployment Steps

1. Start Minikube
```bash
minikube start
```

2. Deploy the application using Helm
```bash
git clone https://github.com/prakhar-8999/cosmocloud-deploy
cd cosmocloud-deploy
helm install testapp . --atomic --timeout 30s
```

3. Verify the deployment
```bash
kubectl get pods
kubectl get services
```

4. Get the frontend URL
```bash
minikube service frontend-svc --url
```

## Architecture Overview

```
External Users
      ↓
Frontend Service (NodePort:31000)
      ↓
Backend Service (ClusterIP:8000)
      ↓
Redis Service (ClusterIP:6379)
```

## Service Configuration

### Frontend Service (NodePort)
```yaml
type: NodePort
port: 5175
nodePort: 31000
```

### Backend Service (ClusterIP)
```yaml
type: ClusterIP
port: 8000
```

### Redis Service (ClusterIP)
```yaml
type: ClusterIP
port: 6379
```

## Screenshots

### Deployment Screenshots
![Deployment](https://raw.githubusercontent.com/prakhar-8999/readmeImages/main/cosmocloud/deployed.png)
- Shows successful deployment of all services

### Pod Status
![Pod Status](https://raw.githubusercontent.com/prakhar-8999/readmeImages/main/cosmocloud/running_pods.png)
- Displays running pods

### Service Logs
![Service logs](https://raw.githubusercontent.com/prakhar-8999/readmeImages/main/cosmocloud/logs.png)
- Displays Service Logs

### Frontend Service Page
![Frontend served Page](https://raw.githubusercontent.com/prakhar-8999/readmeImages/main/cosmocloud/frontend.png)
- Displays local served page


## Troubleshooting

1. If pods are not starting:
```bash
kubectl describe pod <pod-name>
```

2. To check service endpoints:
```bash
kubectl get endpoints
```

3. To view logs:
```bash
kubectl logs <pod-name>
```

## Development

To modify the deployment:

1. Update values in `values.yaml`
2. Upgrade the release:
```bash
helm upgrade testapp ./cosmocloud-deploy
```

## Clean Up

To remove the deployment:
```bash
helm uninstall testapp
minikube stop
```

## Notes

- The frontend is accessible through NodePort 31000
- Backend service is internal and not directly accessible
- Redis persistence is not configured (data will be lost on pod restart)
- All services are deployed in the default namespace
- Environment variables are configured for proper service communication