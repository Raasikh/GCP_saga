
# 🌐 GCP Load Balancer Lab - NGINX on Compute Engine

Welcome to my lab implementation for **Episode 2 of "8 Reasons Why GCP"**!  
This project demonstrates how to build a fault-tolerant, scalable architecture using:
- **Compute Engine VMs** with NGINX web servers
- **Managed Instance Groups (MIGs)**
- **HTTP Load Balancer**

---

## 🚀 Overview
In this lab:
✅ I created an instance template with a startup script to auto-deploy NGINX.  
✅ Launched a managed instance group with two VMs for scalability and fault tolerance.  
✅ Configured an HTTP Load Balancer to distribute incoming web traffic evenly.  
✅ Created health checks, firewall rules, URL maps, and forwarding rules for a seamless setup.

---

## 📊 Architecture
```
[Replace this with an architecture diagram if you have one! Example below:]
- [Client] ---> [HTTP Load Balancer] ---> [Managed Instance Group: 2 NGINX Web Servers]
```

---

## 🖥️ Cloud Shell Commands

### 1️⃣ Create Startup Script
```bash
cat << EOF > startup.sh
#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"$HOSTNAME"'/' /var/www/html/index.nginx-debian.html
EOF
```

### 2️⃣ Create an Instance Template
```bash
gcloud compute instance-templates create nucleus-template   --machine-type=e2-medium   --metadata-from-file startup-script=startup.sh   --tags=http-server   --image-family=debian-11   --image-project=debian-cloud
```

### 3️⃣ Create Managed Instance Group
```bash
gcloud compute instance-groups managed create nucleus-mig   --base-instance-name=nucleus-webserver   --size=2   --template=nucleus-template   --zone=us-west1-a
```

### 4️⃣ Create Firewall Rule
```bash
gcloud compute firewall-rules create accept-tcp-rule-755   --allow=tcp:80   --target-tags=http-server   --description="Allow port 80 traffic"   --direction=INGRESS
```

### 5️⃣ Create Health Check
```bash
gcloud compute health-checks create http nucleus-health-check --port 80
```

### 6️⃣ Create Backend Service & Attach Instance Group
```bash
gcloud compute backend-services create nucleus-backend-service   --protocol=HTTP --health-checks=nucleus-health-check --global

gcloud compute backend-services add-backend nucleus-backend-service   --instance-group=nucleus-mig   --instance-group-zone=us-west1-a --global
```

### 7️⃣ Set Named Port on Instance Group
```bash
gcloud compute instance-groups managed set-named-ports nucleus-mig   --named-ports=http:80 --zone=us-west1-a
```

### 8️⃣ Create URL Map
```bash
gcloud compute url-maps create nucleus-url-map --default-service=nucleus-backend-service
```

### 9️⃣ Create Target HTTP Proxy
```bash
gcloud compute target-http-proxies create nucleus-http-proxy --url-map=nucleus-url-map
```

### 🔟 Reserve Global IP & Create Forwarding Rule
```bash
gcloud compute addresses create nucleus-http-lb-ip --ip-version=IPV4 --global

gcloud compute forwarding-rules create nucleus-http-forwarding-rule   --address=nucleus-http-lb-ip --global   --target-http-proxy=nucleus-http-proxy --ports=80
```

### 🔎 Get External IP
```bash
gcloud compute addresses describe nucleus-http-lb-ip --global
```

---

## 🎯 Outcome
With this setup:
- Users can access the web service via a single IP.
- Traffic is distributed evenly across the NGINX servers.
- GCP’s load balancer automatically handles failover and scaling.

---
