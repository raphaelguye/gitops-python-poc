# 🚀 GitOps PoC — Python Flask App on Kubernetes with ArgoCD and Minikube

## 📋 Project Overview

This Proof of Concept demonstrates a simple **GitOps workflow**:
- A **Python Flask app** is containerized.
- The app is deployed into a **local Kubernetes cluster** (Minikube).
- **ArgoCD** is used to automatically sync Kubernetes manifests from a **GitHub repository**.
- Manual rebuild of the Docker image is required after app code changes.

✅ Focus: Learn GitOps principles end-to-end with minimal complexity.

---

## 🏗️ Stack and Tools

- **Application**: Python + Flask
- **Containerization**: Docker
- **Cluster**: Minikube (local Kubernetes)
- **GitOps Controller**: ArgoCD
- **CI/CD**: Manual Git + Docker (optional future GitHub Actions)

---

## 📦 Project Structure

```
gitops-python-poc/
├── app/                   # Flask application source code
│   └── app.py
├── Dockerfile              # Dockerfile to containerize the app
├── k8s/                    # Kubernetes manifests
│   ├── deployment.yaml
│   └── service.yaml
└── argocd/                 # ArgoCD Application manifest
    └── app.yaml
```

---

## ⚙️ Prerequisites

- Docker installed and logged in
- Minikube installed and running
- kubectl CLI installed
- (Optional) ArgoCD CLI installed

---

## 🛠️ Setup Instructions

### 1. Start Minikube

```bash
minikube start
```

### 2. Install ArgoCD in Minikube

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Port-forward the ArgoCD UI:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
Access ArgoCD UI at [https://localhost:8080](https://localhost:8080).

Get admin password:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

Login with username `admin`.

---

### 3. Apply ArgoCD Application

```bash
kubectl apply -f argocd/app.yaml
```

This tells ArgoCD to watch the Git repository and deploy the app.

---

## 🔄 Local Development Workflow

### When you modify the app code (`app/app.py`) or Kubernetes manifests (`k8s/deployment.yaml`):

1. **Rebuild and push the Docker image**:

```bash
docker build -t your-docker-username/python-app:latest .
docker push your-docker-username/python-app:latest
```

2. **Commit and push your Git changes**:

```bash
git add .
git commit -m "Update app or trigger redeploy"
git push origin main
```

3. **ArgoCD detects Git changes** and auto-syncs to redeploy the app.

4. **Access the app**:

```bash
minikube service python-app-service --url
```

✅ Your updated Flask app is now live!

---

## 📣 Notes

- **Git is the source of truth**: ArgoCD reacts to Git changes, not Docker Registry changes.
- **Auto-sync** must be enabled in `argocd/app.yaml`.
- **Polling** interval must be configured for automatic Git checks (default 30s in PoC).

---

## 🧠 Future Improvements (optional)

- Add GitHub Actions to automatically build & push Docker images.
- Tag Docker images per commit (`v1`, `v2`, `v3`, etc.).
- Use GitHub Webhooks for instant GitOps syncing.
- Implement ArgoCD Image Updater for automatic deployments without YAML changes.

---

## 📜 Quick Commands Cheat Sheet

```bash
# Start Minikube
minikube start

# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Apply ArgoCD App
kubectl apply -f argocd/app.yaml

# Rebuild & Push new Docker image
docker build -t your-docker-username/python-app:latest .
docker push your-docker-username/python-app:latest

# Commit & Push Git changes
git add .
git commit -m "Trigger redeploy"
git push origin main

# Get App URL
minikube service python-app-service --url
```

---

# 🎯 Goal Achieved

**A simple, complete, real-world GitOps learning flow — ready to extend into production practices later!**
