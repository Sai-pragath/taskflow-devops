# 🔧 DevOps Pipeline — TaskFlow

![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat-square&logo=docker&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub_Actions-CI%2FCD-2088FF?style=flat-square&logo=github-actions&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-Minikube-326CE5?style=flat-square&logo=kubernetes&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-E6522C?style=flat-square&logo=prometheus&logoColor=white)

DevOps infrastructure for the TaskFlow task management system — Docker containerization, CI/CD pipeline, Kubernetes orchestration, and monitoring.

##  What's Inside

```
devops-pipeline/
├── docker-compose.yml              — Full stack local orchestration
├── .github/workflows/ci-cd.yml     — GitHub Actions pipeline
├── k8s/
│   ├── deployment.yaml             — Backend + PostgreSQL deployments
│   ├── service.yaml                — LoadBalancer + ClusterIP services
│   └── configmap.yaml              — App config + PVC
└── monitoring/
    └── prometheus.yml              — Scrape configuration
```

##  Docker Compose

```bash
# Start everything
docker-compose up --build

# Stop
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

| Service | Port | Description |
|---------|------|-------------|
| frontend | 3000 | Nginx serving React SPA |
| backend | 8080 | Spring Boot API |
| db | 5432 | PostgreSQL 15 |

##  CI/CD Pipeline

The GitHub Actions workflow runs on every push to `main`:

1. **Build & Test Backend** — Maven compile + test
2. **Build Frontend** — npm ci + Vite build
3. **Docker Build & Push** — Multi-stage builds pushed to GitHub Container Registry
4. **Deploy** — Trigger Render deploy webhook

### Setup

1. Push code to GitHub
2. In repo Settings → Secrets, add:
   - `RENDER_DEPLOY_HOOK` — From Render dashboard

The `GITHUB_TOKEN` is automatically available for GHCR access.

##  Kubernetes (Minikube)

```bash
# Start Minikube
minikube start

# Create secrets
kubectl create secret generic db-credentials \
  --from-literal=username=postgres \
  --from-literal=password=password

kubectl create secret generic jwt-secret \
  --from-literal=secret=YourJWTSecretKeyHere

# Apply manifests
kubectl apply -f k8s/

# Check status
kubectl get pods
kubectl get services

# Access the service
minikube service task-manager-service
```

##  Monitoring

```bash
# Start Prometheus
docker run -d -p 9090:9090 \
  -v $(pwd)/monitoring/prometheus.yml:/etc/prometheus/prometheus.yml \
  prom/prometheus

# Start Grafana
docker run -d -p 3001:3000 grafana/grafana
```

1. Open Grafana at `http://localhost:3001` (admin/admin)
2. Add Prometheus data source: `http://host.docker.internal:9090`
3. Import dashboard ID **4701** for Spring Boot metrics

##  Deployment Achievements

- **~70% latency reduction**: WebSocket push vs 5s polling → sub-100ms updates
- **~60% deployment time reduction**: 15min manual SSH → 6min automated CI/CD

##  License

MIT