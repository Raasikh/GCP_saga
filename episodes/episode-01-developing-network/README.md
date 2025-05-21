# 🚀 Episode 1: Developing Your Google Cloud Network

**Part of the `8 Reasons Why GCP` Series**  
This lab simulates a real-world GCP setup involving networking, databases, Kubernetes, IAM, and monitoring.

---

## 🛠️ Tools Used
- Google Cloud Console / Cloud Shell
- Kubernetes Engine (GKE)
- Cloud SQL (MySQL)
- IAM
- Monitoring
- `gsutil`, `gcloud`, `kubectl`

---

## 📦 Tasks + Commands

### 🔹 Task 1: Create Development VPC & Subnets
```bash
gcloud compute networks create griffin-dev-vpc \
    --subnet-mode=custom

gcloud compute networks subnets create griffin-dev-wp \
    --network=griffin-dev-vpc \
    --region=REGION \
    --range=192.168.16.0/20

gcloud compute networks subnets create griffin-dev-mgmt \
    --network=griffin-dev-vpc \
    --region=REGION \
    --range=192.168.32.0/20
```

### 🔹 Task 2: Create Production VPC & Subnets
```bash
gcloud compute networks create griffin-prod-vpc \
    --subnet-mode=custom

gcloud compute networks subnets create griffin-prod-wp \
    --network=griffin-prod-vpc \
    --region=REGION \
    --range=192.168.48.0/20

gcloud compute networks subnets create griffin-prod-mgmt \
    --network=griffin-prod-vpc \
    --region=REGION \
    --range=192.168.64.0/20
```

### 🔹 Task 3: Create Bastion Host
```bash
gcloud compute instances create bastion-host \
    --zone=ZONE \
    --machine-type=e2-micro \
    --network-interface subnet=griffin-dev-mgmt \
    --network-interface subnet=griffin-prod-mgmt \
    --tags=ssh \
    --image-family=debian-11 \
    --image-project=debian-cloud
```

### 🔹 Task 4: Create Cloud SQL Instance + DB
```bash
gcloud sql instances create griffin-dev-db \
    --database-version=MYSQL_5_7 \
    --tier=db-f1-micro \
    --region=REGION

gcloud sql users set-password root \
    --host=% \
    --instance=griffin-dev-db \
    --password=YOUR_ROOT_PASSWORD
```

#### Inside SQL Console:
```sql
CREATE DATABASE wordpress;
CREATE USER "wp_user"@"%" IDENTIFIED BY "stormwind_rules";
GRANT ALL PRIVILEGES ON wordpress.* TO "wp_user"@"%";
FLUSH PRIVILEGES;
```

### 🔹 Task 5: Create Kubernetes Cluster
```bash
gcloud container clusters create griffin-dev \
    --zone=ZONE \
    --num-nodes=2 \
    --machine-type=e2-standard-4 \
    --network=griffin-dev-vpc \
    --subnetwork=griffin-dev-wp
```

### 🔹 Task 6: Prepare the Cluster
```bash
gsutil cp -r gs://cloud-training/gsp321/wp-k8s .
cd wp-k8s

# After editing wp-env.yaml with DB credentials:
kubectl apply -f wp-env.yaml

gcloud iam service-accounts keys create key.json \
  --iam-account=cloud-sql-proxy@$GOOGLE_CLOUD_PROJECT.iam.gserviceaccount.com

kubectl create secret generic cloudsql-instance-credentials \
  --from-file key.json
```

### 🔹 Task 7: Deploy WordPress
> Replace `YOUR_SQL_INSTANCE` with actual instance connection name.
```bash
kubectl apply -f wp-deployment.yaml
kubectl apply -f wp-service.yaml
```

### 🔹 Task 8: Enable Uptime Monitoring
- Create uptime check in **Cloud Monitoring** UI
- Use **WordPress LoadBalancer IP** as target

### 🔹 Task 9: Provide Access to Another Engineer
```bash
gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT \
  --member=user:second-user@gmail.com \
  --role=roles/editor
```

---

## ✅ Final Outcome
By the end of this lab, we successfully:
- Built separate dev/prod VPCs
- Used a bastion host to manage access securely
- Configured Cloud SQL + WordPress integration
- Deployed WordPress on GKE
- Enabled real-time monitoring
- Simulated real team collaboration using IAM

---

**🔗 Stay tuned for Episode 2: Building a Secure Google Cloud Network!**

