# The Implemented DevOps Workforce in Google Cloud – Part 1

## 🚀 Overview

In this part, we lay the foundation for a robust CI/CD pipeline on Google Cloud using only **native GCP tools**. Our project goal: automate deployment of a simple Go application using GitHub, Cloud Build, Artifact Registry, and GKE.

---

## 🛠️ Technologies Used

- **Google Kubernetes Engine (GKE)**
- **Artifact Registry**
- **Cloud Build**
- **GitHub**
- **Cloud Shell**

---

## ⚙️ Steps Covered

### 1. Enable Required APIs
```bash
gcloud services enable container.googleapis.com cloudbuild.googleapis.com
```

### 2. Grant Permissions to Cloud Build
```bash
PROJECT_ID=$(gcloud config get-value project)
gcloud projects add-iam-policy-binding $PROJECT_ID \
  --member=serviceAccount:$(gcloud projects describe $PROJECT_ID --format="value(projectNumber)")@cloudbuild.gserviceaccount.com \
  --role="roles/container.developer"
```

### 3. Configure GitHub in Cloud Shell
```bash
curl -sS https://webi.sh/gh | sh
gh auth login
git config --global user.name "your-github-username"
git config --global user.email "your-email@example.com"
```

### 4. Clone Sample Code
```bash
git clone https://github.com/your-username/sample-app.git
gsutil cp -r gs://spls/gsp330/sample-app/* sample-app
```

### 5. Create Artifact Registry
```bash
gcloud artifacts repositories create my-repository \
  --repository-format=docker \
  --location=us-east1 \
  --description="Docker repo"
```

### 6. Create GKE Cluster
```bash
gcloud container clusters create hello-cluster \
  --zone us-east1-c \
  --num-nodes=3 \
  --enable-autoscaling --min-nodes=2 --max-nodes=6 \
  --release-channel=regular
```

### 7. Create `prod` and `dev` namespaces
```bash
kubectl create namespace prod
kubectl create namespace dev
```

---

## 🧪 Cloud Build Triggers

### Trigger 1: Production
- **Name:** `sample-app-prod-deploy`
- **Branch:** `^master$`
- **Config:** `cloudbuild.yaml`

### Trigger 2: Development
- **Name:** `sample-app-dev-deploy`
- **Branch:** `^dev$`
- **Config:** `cloudbuild-dev.yaml`

These triggers ensure automated builds and deployments when code is pushed to GitHub.


# Part 2: The Implemented DevOps Workforce in Google Cloud

## Overview
This part focuses on the actual deployment process triggered via GitHub commits and managed through Cloud Build Triggers. We walk through deploying to GKE, setting up Load Balancer Services, and validating the running application.

---

## ✅ Tasks Covered

### 1. Configure Cloud Build YAMLs
- Updated `cloudbuild.yaml` and `cloudbuild-dev.yaml` to set version tags (e.g., `v1.0`)
- Replaced `<PROJECT_ID>` with actual project ID
- Ensured Docker images match between YAML files and Kubernetes manifests

### 2. Deployment to GKE via GitHub Commits
- Pushed changes to `dev` branch triggers `cloudbuild-dev.yaml`
- Pushed changes to `master` branch triggers `cloudbuild.yaml`

### 3. Kubernetes Deployment
```bash
kubectl apply -f dev/deployment.yaml -n dev
kubectl apply -f prod/deployment.yaml -n prod
```

### 4. Expose Services
```bash
kubectl expose deployment development-deployment   --type=LoadBalancer   --name=dev-deployment-service   --port=8080   --target-port=8080   -n dev

kubectl expose deployment production-deployment   --type=LoadBalancer   --name=prod-deployment-service   --port=8080   --target-port=8080   -n prod
```

### 5. Validate Deployments
Access via:
```
http://<EXTERNAL-IP>:8080/blue
```

---

## 🧪 Rollback Strategy
- Use GKE dashboard or `kubectl rollout undo` for quick rollback.

---

## 🔗 Final Thoughts
This completes the end-to-end DevOps pipeline using GitHub, Cloud Build, Artifact Registry, and GKE. Fully automated, scalable, and production-ready.
