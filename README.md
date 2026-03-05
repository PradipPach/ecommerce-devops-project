# 🚀 **Real-World DevOps Project: End-to-End Cloud-Native Application on Kubernetes**

## ✅ **Project Title:**

**Cloudnautic Shop – Scalable E-Commerce Microservices with Full DevOps Automation**

---

# 🧩 **1. Problem Statement (Real Company Scenario)**

A startup wants to build an **e-commerce application** with:

* High availability
* Zero downtime deployments
* Automated CI/CD
* GitOps-driven configuration
* Infrastructure as Code
* Monitoring, logging & alerting
* Secure, cost-optimized architecture
* Rollbacks & disaster recovery

They want a **complete DevOps setup from scratch**.

---

# 🏗️ **2. High-Level Architecture**

### Components:

* **Frontend** → React/Next.js
* **Backend API** → Node.js / Python Flask
* **Database** → AWS RDS (MySQL/Postgres)
* **Container Registry** → Docker Hub / ECR
* **Infrastructure** → AWS with Terraform

  * VPC, Subnets, SG
  * EKS Cluster
  * RDS
  * ALB Ingress
* **CI/CD** → GitHub Actions / Jenkins
* **Monitoring** → Prometheus + Grafana
* **Logging** → EFK (Elasticsearch + Fluentd + Kibana)
* **Secrets** → AWS Secrets Manager
* **Autoscaling** → HPA based on metrics
* **Alerting** → Slack + Grafana Alerts

---

# 📁 **3. GitHub Repository Structure (Industry Standard)**

```
cloudnautic-shop/
├── frontend/
│   ├── Dockerfile
│   └── src/
├── backend/
│   ├── app.py / index.js
│   ├── requirements.txt / package.json
│   ├── Dockerfile
│   └── config/
├── infra/
│   └── terraform/
│       ├── vpc/
│       ├── eks/
│       ├── rds/
│       ├── alb/
│       └── variables.tf
├── k8s/
│   ├── namespaces/
│   ├── deployments/
│   ├── services/
│   ├── ingress/
│   ├── configmaps/
│   └── secrets/
├── monitoring/
│   ├── prometheus/
│   └── grafana/
├── logging/
│   ├── fluentd/
│   └── es-kibana/
├── .github/workflows/
│   ├── build.yml
│   └── deploy.yml
└── README.md
```

---

# ⚙️ **4. Infrastructure as Code (Terraform)**

### **Terraform Features**

* Provision AWS VPC
* Deploy EKS cluster
* Setup node groups
* Create RDS database
* Configure IAM roles
* Attach ALB ingress controller

---

### **Sample Terraform Code – EKS Cluster**

```hcl
module "eks" {
  source          = "terraform-aws-modules/eks/aws"
  cluster_name    = "cloudnautic-shop"
  cluster_version = "1.30"
  
  vpc_id          = module.vpc.vpc_id
  subnet_ids      = module.vpc.private_subnets

  node_groups = {
    default = {
      desired_capacity = 2
      instance_type    = "t3.medium"
    }
  }
}
```

---

# 🐳 **5. Application Containerization (Docker)**

### Backend Dockerfile:

```dockerfile
FROM python:3.10
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

### Frontend Dockerfile:

```dockerfile
FROM node:20
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
RUN npm run build
CMD ["npm", "start"]
```

---

# ☸️ **6. Kubernetes Deployment**

### **backend-deploy.yaml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: backend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend
  template:
    metadata:
      labels:
        app: backend
    spec:
      containers:
      - name: backend
        image: pradippachapol/cloudnautic-backend:latest
        ports:
        - containerPort: 5000
```

### **Service**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-svc
spec:
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 5000
  type: ClusterIP
```

### **Ingress**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: cloudnautic-ingress
spec:
  rules:
  - host: shop.cloudnautic.in
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-svc
            port:
              number: 80
```

---

# 🔁 **7. CI/CD Pipeline (GitHub Actions)**

---

## **CI Pipeline – build.yml**

Builds Docker images & pushes to Docker Hub.

```yaml
name: Build

on:
  push:
    branches: ["main"]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3

    - name: Login to DockerHub
      run: echo ${{ secrets.DOCKER_PASS }} | docker login -u ${{ secrets.DOCKER_USER }} --password-stdin

    - name: Build Backend Image
      run: docker build -t pradippachapol/cloudnautic-backend:latest backend/

    - name: Push Backend Image
      run: docker push pradippachapol/cloudnautic-backend:latest
```

---

## **CD Pipeline – deploy.yml**

Deploys to EKS automatically.

```yaml
name: Deploy to EKS

on:
  workflow_run:
    workflows: ["Build"]
    types: [completed]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Configure AWS
        uses: aws-actions/configure-aws-credentials@v3
        with:
          aws-access-key-id: ${{ secrets.AWS_KEY }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET }}
          aws-region: ap-south-1

      - name: Update kubeconfig
        run: aws eks update-kubeconfig --name cloudnautic-shop

      - name: Deploy to Cluster
        run: kubectl apply -f k8s/
```

---

# 📊 **8. Monitoring (Prometheus + Grafana)**

* Node exporter
* Kube-state-metrics
* Prometheus server
* Grafana dashboards
* Alerts for CPU, memory, latency, API errors

---

# 📜 **9. Logging (EFK Stack)**

* Fluentd Daemonset
* Elasticsearch cluster
* Kibana dashboard

---

# 🔐 **10. Security**

* AWS Secrets Manager for DB password
* KMS encryption for RDS
* Private subnets for EKS nodes
* Network Policies in Kubernetes
* TLS-enabled ingress
* IAM roles for service accounts

---

# 🌀 **11. Deployment Strategies**

Supported:

* Rolling updates
* Blue-Green
* Canary (Argo Rollouts optional)

---

# 🧪 **12. Testing Layer**

* Unit tests (pytest/jest)
* Integration tests
* Load testing with Locust
* Security scanning with Trivy
* SAST (CodeQL)

---

# 🧯 **13. Disaster Recovery**

* Automated DB backups
* Cross-region snapshot copy
* Restore tests
* GitHub release-based versioning
* kubectl diff before deployment

---
