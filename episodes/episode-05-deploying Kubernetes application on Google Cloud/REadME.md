# 🚀 Episode 5 – Deploying Kubernetes Applications on Google Cloud

This project is part of my **“8 Reasons Why GCP”** series. In this episode, we containerize a Go application using Docker, push the image to Google Cloud's Artifact Registry, and deploy it on Google Kubernetes Engine (GKE).

---

## 🛠️ What You'll Learn

- Create a Docker image from a Go app
- Push it to Artifact Registry
- Deploy using Kubernetes manifests
- Expose the app via LoadBalancer
- Access the app using Web Preview

---

## 📦 Prerequisites

- Docker & GCP SDK installed
- Google Cloud project with billing enabled
- Kubernetes Engine API enabled

---

## 🚧 Steps Summary

### 1. Clone and extract the app

```bash
gsutil cp gs://spls/gsp318/valkyrie-app.tgz .
tar -xzf valkyrie-app.tgz
cd valkyrie-app
```

### 2. Create a Dockerfile

```Dockerfile
FROM golang:1.10
WORKDIR /go/src/app
COPY source .
RUN go install -v
ENTRYPOINT ["app", "-single=true", "-port=8080"]
```

Build the image:
```bash
docker build -t valkyrie-prod:v0.0.1 .
```

---

### 3. Run the container locally

```bash
docker run -p 8080:8080 valkyrie-prod:v0.0.1 &
```

---

### 4. Push to Artifact Registry

```bash
gcloud auth configure-docker us-east1-docker.pkg.dev

docker tag valkyrie-prod:v0.0.1   us-east1-docker.pkg.dev/YOUR_PROJECT_ID/valkyrie-docker-repo/valkyrie-prod:v0.0.1

docker push us-east1-docker.pkg.dev/YOUR_PROJECT_ID/valkyrie-docker-repo/valkyrie-prod:v0.0.1
```

---

### 5. Deploy to Kubernetes

```bash
gcloud container clusters get-credentials valkyrie-dev --zone us-east1-c
```

Edit `k8s/deployment.yaml`:
```yaml
image: us-east1-docker.pkg.dev/YOUR_PROJECT_ID/valkyrie-docker-repo/valkyrie-prod:v0.0.1
```

Apply manifests:

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

---

### 6. Access the App

```bash
kubectl get services
```

Visit the `EXTERNAL-IP` in your browser to view the running app.

---

## 📁 Repo Contents

- `Dockerfile`
- `k8s/deployment.yaml`
- `k8s/service.yaml`
- `source/` – Go application source code

---

## 📺 Video & Walkthrough

Available on LinkedIn + GitHub discussions.

---

## 🙌 Credits

Lab based on Qwiklabs GSP318.  
Built using Docker, GKE, and Artifact Registry on GCP.
